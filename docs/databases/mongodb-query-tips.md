# Mongoose / MongoDB: Senior Developer Tips & Tricks

Practical habits, patterns, and shortcuts experienced developers use daily to write faster, safer, and more maintainable MongoDB/Mongoose code.

---

## Table of Contents

1. [Performance Habits](#performance-habits)
2. [Query Debugging & Profiling](#query-debugging--profiling)
3. [Schema Design Patterns](#schema-design-patterns)
4. [Safer Update Patterns](#safer-update-patterns)
5. [Avoiding Common Pitfalls](#avoiding-common-pitfalls)
6. [Reusable Query Utilities](#reusable-query-utilities)
7. [Efficient Population Strategies](#efficient-population-strategies)
8. [Bulk Operations](#bulk-operations)
9. [Error Handling Patterns](#error-handling-patterns)
10. [Testing & Local Dev Speedups](#testing--local-dev-speedups)
11. [Production Safety Checklist](#production-safety-checklist)
12. [Handy One-Liners](#handy-one-liners)

---

## Performance Habits

**Default to `.lean()` for read-only paths.**
```js
const users = await User.find({ role: 'user' }).lean();
```
Skips hydration into full Mongoose documents — noticeably faster for API responses, reports, and anything you're not going to `.save()`.

**Project only what you need.**
```js
await User.find({ role: 'admin' }).select('name email').lean();
```
Reduces network payload and memory — matters a lot at scale.

**Prefer `countDocuments()` over `find().length`**, and `estimatedDocumentCount()` when you don't need a filtered count (it uses collection metadata and is much faster).
```js
await User.estimatedDocumentCount(); // fast, no filter
await User.countDocuments({ role: 'admin' }); // accurate, filtered
```

**Use `.cursor()` for large datasets** instead of loading everything into memory.
```js
const cursor = User.find({ active: true }).cursor();
for await (const doc of cursor) {
  // process one at a time
}
```

**Batch writes instead of looping `save()` or `updateOne()` in a loop.** See [Bulk Operations](#bulk-operations).

**Cache expensive/read-heavy aggregations** (e.g., dashboards, counts) with Redis or in-memory TTL cache rather than recomputing on every request.

---

## Query Debugging & Profiling

**`.explain()` is your best friend.**
```js
const plan = await User.find({ email: 'a@mail.com' }).explain('executionStats');
console.log(plan.executionStats.executionTimeMillis, plan.executionStats.totalDocsExamined);
```
If `totalDocsExamined` is much higher than `nReturned`, you're missing an index.

**Enable query logging in development.**
```js
mongoose.set('debug', true); // logs every query Mongoose sends to MongoDB
```
Or with a custom formatter:
```js
mongoose.set('debug', (collectionName, method, ...args) => {
  console.log(`${collectionName}.${method}`, JSON.stringify(args));
});
```

**Use MongoDB Compass or `mongosh`'s `db.collection.stats()`** to inspect index usage, storage size, and document count without touching app code.

**Slow query log** — enable profiling on a dev/staging DB to catch slow queries early:
```js
db.setProfilingLevel(1, { slowms: 100 }); // log queries slower than 100ms
db.system.profile.find().sort({ ts: -1 }).limit(10);
```

---

## Schema Design Patterns

**Embed vs Reference — the rule of thumb:**
- Embed when data is small, bounded, and always accessed together (e.g., address inside a user).
- Reference (`populate`) when data is large, unbounded, shared across documents, or updated independently (e.g., posts by an author).

**Avoid unbounded arrays** growing inside a single document (MongoDB has a 16MB document limit). If a list can grow indefinitely (comments, logs, events), use a separate collection with a reference field instead.

**Use `timestamps: true`** instead of manually managing `createdAt`/`updatedAt` — one less thing to forget.

**Add indexes at schema-definition time, not as an afterthought:**
```js
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ createdAt: -1 }); // fast recent-first queries
userSchema.index({ role: 1, active: 1 }); // compound index for common filter combos
```
Compound index field order matters — put the most selective / most-filtered-on field first, matching your actual query patterns.

**Use discriminators for polymorphic collections** instead of one giant schema with dozens of optional fields:
```js
const options = { discriminatorKey: 'kind' };
const eventSchema = new Schema({ name: String }, options);
const Event = model('Event', eventSchema);

const ClickEvent = Event.discriminator('Click', new Schema({ x: Number, y: Number }, options));
```

---

## Safer Update Patterns

**Always scope updates with a specific filter** — never rely on accidentally-broad queries. Double-check `updateMany`/`deleteMany` filters in a `find()` first before running the mutating version.

```js
// Sanity-check before destructive ops
const affected = await User.countDocuments({ role: 'guest', lastLogin: { $lt: cutoff } });
console.log(`Will delete ${affected} users`);
// then run deleteMany with the same filter
```

**Use `findOneAndUpdate` with `{ new: true, runValidators: true }`** so you get the updated doc back *and* schema validation still applies (Mongoose doesn't run validators on updates by default).

**Use `$setOnInsert` with upserts** to only set fields on creation, not on every update:
```js
await User.updateOne(
  { email },
  { $set: { lastLogin: new Date() }, $setOnInsert: { createdAt: new Date() } },
  { upsert: true }
);
```

**Prefer atomic operators (`$inc`, `$push`, `$addToSet`) over read-modify-write** to avoid race conditions:
```js
// Risky: read, modify in JS, then save — another request could interleave
const user = await User.findById(id);
user.credits += 10;
await user.save();

// Better: atomic, race-condition-safe
await User.updateOne({ _id: id }, { $inc: { credits: 10 } });
```

---

## Avoiding Common Pitfalls

- **Forgetting `await`** on a Mongoose query — it returns a thenable Query object, but silent bugs happen if you forget to await it in a conditional or loop.
- **Using `find()` when you mean `findOne()`** — `find()` always returns an array, even for zero/one match.
- **Mutating query results with `.lean()` and expecting instance methods/virtuals to work** — `.lean()` gives plain objects, not Mongoose documents, so `.save()`, virtuals, and methods won't exist.
- **Not validating ObjectId format before querying** — an invalid string passed to `findById` throws a `CastError`. Guard with `mongoose.Types.ObjectId.isValid(id)` when the ID comes from user input.
- **Relying on default validators during `updateOne`/`updateMany`** — they don't run unless `runValidators: true` is set.
- **Storing dates as strings** instead of `Date` — breaks range queries and sorting. Always use the `Date` type in schemas.
- **Overusing `$where` and heavy regex without anchors** — both are slow and often bypass indexes entirely. Prefer indexed fields and anchored regex (`^prefix`) when possible.
- **N+1 query problems** — looping and querying inside a `for` loop instead of a single `$in` query or one `populate()` call.
```js
// Bad: N+1
for (const post of posts) {
  post.author = await User.findById(post.authorId);
}

// Good: one query
const authorIds = posts.map(p => p.authorId);
const authors = await User.find({ _id: { $in: authorIds } }).lean();
```

---

## Reusable Query Utilities

**A small pagination helper** to avoid repeating skip/limit logic everywhere:
```js
async function paginate(model, filter = {}, { page = 1, limit = 20, sort = '-createdAt', select } = {}) {
  const [data, total] = await Promise.all([
    model.find(filter).select(select).sort(sort).skip((page - 1) * limit).limit(limit).lean(),
    model.countDocuments(filter),
  ]);
  return { data, total, page, pages: Math.ceil(total / limit) };
}
```

**A generic "not found" wrapper** to avoid repeating null checks:
```js
async function findOrThrow(model, filter, message = 'Not found') {
  const doc = await model.findOne(filter);
  if (!doc) {
    const err = new Error(message);
    err.status = 404;
    throw err;
  }
  return doc;
}
```

**Soft delete plugin pattern** (reusable across schemas):
```js
function softDeletePlugin(schema) {
  schema.add({ deletedAt: { type: Date, default: null } });
  schema.pre(/^find/, function (next) {
    if (!this.getQuery().includeDeleted) this.where({ deletedAt: null });
    next();
  });
  schema.methods.softDelete = function () {
    this.deletedAt = new Date();
    return this.save();
  };
}
userSchema.plugin(softDeletePlugin);
```

---

## Efficient Population Strategies

**Select only needed fields in populate** to avoid over-fetching:
```js
await Post.find().populate('author', 'name email').lean();
```

**Use `$lookup` in aggregation instead of `populate()`** when you need joins combined with grouping/filtering — it's a single round trip and more powerful:
```js
await Post.aggregate([
  { $lookup: { from: 'users', localField: 'author', foreignField: '_id', as: 'author' } },
  { $unwind: '$author' },
  { $match: { 'author.role': 'admin' } },
]);
```

**Avoid deeply nested populate chains** in hot paths — each level is a separate query. If you're populating 3+ levels deep regularly, consider denormalizing a summary field instead (e.g., store `authorName` directly on the post, updated via a hook).

---

## Bulk Operations

**`bulkWrite` for mixed batch operations** — far faster than looping individual calls:
```js
await User.bulkWrite([
  { updateOne: { filter: { _id: id1 }, update: { $set: { active: true } } } },
  { updateOne: { filter: { _id: id2 }, update: { $inc: { credits: 5 } } } },
  { deleteOne: { filter: { _id: id3 } } },
  { insertOne: { document: { name: 'New User', email: 'new@mail.com' } } },
]);
```

**`insertMany` with `ordered: false`** to continue inserting remaining docs even if one fails (useful for large imports):
```js
await User.insertMany(bigArray, { ordered: false });
```

---

## Error Handling Patterns

**Catch duplicate key errors specifically** (MongoDB error code `11000`):
```js
try {
  await User.create({ email: 'dup@mail.com' });
} catch (err) {
  if (err.code === 11000) {
    throw new Error('Email already exists');
  }
  throw err;
}
```

**Distinguish `CastError` (bad ObjectId/type) from `ValidationError`** for clean API error responses:
```js
if (err.name === 'CastError') return res.status(400).json({ error: 'Invalid ID format' });
if (err.name === 'ValidationError') return res.status(422).json({ error: err.message });
```

---

## Testing & Local Dev Speedups

- Use **`mongodb-memory-server`** for fast, isolated unit/integration tests without a real DB connection.
- Reset test DB state with `await Promise.all(collections.map(c => c.deleteMany({})))` between test suites instead of dropping the whole DB.
- Use `.toObject()` or `.lean()` when asserting equality in tests to avoid comparing Mongoose document internals.
- Seed scripts: keep a `seed.js` that populates realistic fixture data — saves time re-creating test data manually every session.

---

## Production Safety Checklist

- [ ] Every collection has indexes matching its real query patterns (`.explain()` regularly, especially after schema changes).
- [ ] All `updateMany`/`deleteMany` calls have been sanity-checked against a `find`/`countDocuments` with the same filter before shipping.
- [ ] Connection pool size (`maxPoolSize`) is tuned for expected concurrency, not left at default under heavy load.
- [ ] Sensitive fields (passwords, tokens) are excluded via schema-level `select: false` and never accidentally returned:
```js
password: { type: String, select: false }
// explicitly include when needed:
await User.findOne({ email }).select('+password');
```
- [ ] Long-running aggregations have a `maxTimeMS` set to avoid hanging queries:
```js
await User.aggregate([...]).option({ maxTimeMS: 5000 });
```
- [ ] Backups/snapshots exist before running any one-off migration script in production.

---

## Handy One-Liners

```js
// Toggle a boolean atomically
await User.updateOne({ _id: id }, [{ $set: { active: { $not: '$active' } } }]);

// Get a random sample of documents
await User.aggregate([{ $sample: { size: 5 } }]);

// Find duplicates by a field
await User.aggregate([
  { $group: { _id: '$email', count: { $sum: 1 }, ids: { $push: '$_id' } } },
  { $match: { count: { $gt: 1 } } },
]);

// Copy a document (minus _id)
const { _id, ...rest } = original.toObject();
await User.create(rest);

// Convert string to ObjectId safely
const isValidId = mongoose.Types.ObjectId.isValid(someId);

// Quick "does this exist" check without fetching the whole doc
const exists = await User.exists({ email });
```

---

## Quick Mental Checklist Before Writing Any Query

1. Do I need the full document, or just a few fields? → use `.select()`
2. Am I only reading (not saving)? → use `.lean()`
3. Is this filter backed by an index? → check with `.explain()`
4. Could this run in a loop and cause N+1 queries? → batch with `$in` or `bulkWrite`
5. Is this an `updateMany`/`deleteMany`? → verify the filter with `countDocuments` first
6. Does this update need validation? → add `runValidators: true`