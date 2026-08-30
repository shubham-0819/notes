# Mongoose / MongoDB: Tricky Interview Questions & Edge Cases

A collection of conceptual traps, edge-case behaviors, and "gotcha" interview questions — the kind that separate people who've read the docs from people who've actually been burned in production.

---

## Table of Contents

1. [Core Conceptual Questions](#core-conceptual-questions)
2. [Query Behavior Edge Cases](#query-behavior-edge-cases)
3. [Update Operator Gotchas](#update-operator-gotchas)
4. [Schema & Validation Traps](#schema--validation-traps)
5. [Population Edge Cases](#population-edge-cases)
6. [Indexing Tricky Questions](#indexing-tricky-questions)
7. [Aggregation Edge Cases](#aggregation-edge-cases)
8. [Transactions & Concurrency](#transactions--concurrency)
9. [Data Modeling Trade-off Questions](#data-modeling-trade-off-questions)
10. [Rapid-Fire "What Happens If…" Round](#rapid-fire-what-happens-if)

---

## Core Conceptual Questions

**Q: What's the difference between `find()` returning `[]` and `findOne()` returning `null`?**
`find()` always resolves to an array — empty if nothing matches, never `null`/`undefined`. `findOne()` resolves to a single document or `null`. A common bug: checking `if (!results)` after `find()` — it's truthy even when empty, so you must check `results.length`.

**Q: Is a Mongoose query a Promise?**
No — it's a **thenable "Query" object**. It has `.then()` so `await` works, but it isn't a real Promise until you call `.exec()` or `await`/`.then()` it. This matters because you can keep chaining methods (`.sort()`, `.select()`) onto it *until* it's awaited/executed — after that, it's locked in.
```js
const q = User.find({ role: 'admin' }); // not executed yet
q.sort({ name: 1 });                    // still chainable
const result = await q;                 // NOW it executes
```

**Q: What does `.lean()` actually change under the hood?**
Returns plain JS objects instead of Mongoose Documents. You lose: virtuals, instance methods, `.save()`, getters/setters, change tracking, and automatic `_id`→string casting behaviors tied to the document proxy. You gain: speed (no hydration overhead), lower memory footprint.

**Q: Why doesn't `runValidators` run by default on update operations?**
Because `updateOne`/`updateMany`/`findOneAndUpdate` operate on the raw MongoDB update, not a full document — Mongoose can't run "required field" checks without knowing the full resulting document, and by default assumes you know what you're doing for partial updates. This is why `$set`-based partial updates can silently violate schema constraints unless you explicitly opt in.

---

## Query Behavior Edge Cases

**Q: `User.findById(undefined)` — what happens?**
This is a classic trap. Passing `undefined` as a filter to `findById`/`findOne` doesn't throw — Mongoose translates it in a way that can effectively become `{ _id: undefined }`... but MongoDB's actual behavior here has changed across driver versions and is a common source of "why did this return a random document" bugs. **Always validate the ID exists and is a valid ObjectId string before querying** — never trust it silently failing safe.

**Q: What's the difference between `{ age: null }` and `{ age: { $exists: false } }` in a query?**
`{ age: null }` matches documents where `age` is explicitly `null` **or** where the field doesn't exist at all (MongoDB treats missing fields as matching `null` in equality queries). `{ age: { $exists: false } }` matches *only* documents where the field is truly absent. This distinction trips people up constantly.

**Q: Does `{ tags: 'js' }` match a document where `tags: ['js', 'node']`?**
Yes. For array fields, an equality match against a scalar checks if that scalar is *an element of the array* — you don't need `$in` or `$elemMatch` for this simple case. `$elemMatch` is only needed when you must match multiple conditions against the *same* array element.

**Q: `User.find({ 'address.city': 'Delhi' })` vs `User.find({ address: { city: 'Delhi' } })` — same result?**
No. Dot notation matches the nested field regardless of other fields in the subdocument. The second form requires the **entire** `address` object to match exactly `{ city: 'Delhi' }` — if `address` also has a `zip` field, it won't match.

**Q: Why might `.select('-password')` still leak the password field?**
If the schema defines `password: { type: String, select: false }`, that's enforced at the schema level and is safer. But if select-exclusion is only done at the query level (`.select('-password')`), any query that forgets to add it will leak the field — including `populate()` calls that don't repeat the exclusion, and `aggregate()` (which bypasses Mongoose query-level select entirely).

---

## Update Operator Gotchas

**Q: What happens if you `$set` and `$inc` the same field in one update?**
MongoDB throws an error — `"Updating the path 'x' would create a conflict at 'x'"`. You can't apply two different top-level update operators to the exact same field in a single update document.

**Q: `updateOne` with an empty update object `{}` — what happens?**
Throws an error: MongoDB requires at least one modifier or a full replacement document. An empty `{}` is ambiguous (not clear if it's meant as “replace with empty doc” vs “no-op”), so it's rejected.

**Q: Does `findOneAndUpdate` guarantee atomicity across the find + update?**
Yes — that's the entire point of the method. It's a single atomic operation on the server; the "find" and "update" are not two separate round trips from the app's perspective. Contrast with doing `findOne()` then `save()` in application code, which is **not** atomic and is vulnerable to race conditions between the read and the write.

**Q: You call `findOneAndUpdate` without `{ new: true }`. What do you get back?**
The document **as it was before the update** (the old version). This is a very common bug — developers expect the updated version by default and get stale data instead.

**Q: `$push` vs `$addToSet` — when would `$addToSet` still insert a duplicate?**
`$addToSet` does a **shallow equality check**. For arrays of objects, `{ id: 1, name: 'a' }` and `{ id: 1, name: 'a' }` (structurally identical) are treated as duplicates, but if key order differs internally or an extra whitespace/type mismatch exists (`id: 1` vs `id: '1'`), it will NOT be recognized as a duplicate and will insert anyway.

---

## Schema & Validation Traps

**Q: Does changing a schema field type retroactively affect existing documents in MongoDB?**
No. MongoDB is schemaless at the storage layer — Mongoose schemas are enforced only at the application layer, on read/write through Mongoose. Existing documents in the collection keep whatever shape they had; only documents you write *through* the new schema get cast/validated against it. This is why schema migrations often require a explicit migration script.

**Q: What does `strict: true` (the default) do, and what's the risk of `strict: false`?**
`strict: true` silently **drops** any fields not defined in the schema when saving. `strict: false` allows arbitrary fields to be persisted. The gotcha: with `strict: true`, if you typo a field name when constructing a document (`usernme` instead of `username`), it fails silently — no error, the field is just dropped.

**Q: Are Mongoose validators run in parallel or series, and does one failing stop the others?**
By default, Mongoose collects **all** validation errors across all fields before rejecting (not fail-fast on the first one) — useful for returning complete form-validation feedback to a client in one round trip.

**Q: `required: true` on a field with a `default` value — can it ever fail validation?**
No — if a `default` is defined, the field will always have a value at validation time, so `required` never triggers. This combination is often a subtle bug: developers think they're both making a field mandatory *and* giving it a fallback, but the fallback means "mandatory" is unreachable.

---

## Population Edge Cases

**Q: If a referenced document has been deleted, what does `populate()` return for that field?**
`null` — Mongoose doesn't throw; the `author` field (or whatever the ref is) simply resolves to `null` if the referenced `_id` no longer exists in the target collection. Code that assumes populated refs are always present (`post.author.name`) will throw a runtime error on orphaned references.

**Q: Can you `populate()` a field that isn't actually a `ref` in the schema?**
No — Mongoose needs the `ref` (or a `refPath` for dynamic refs) defined in the schema to know which model to query. Calling `.populate('someField')` on a field without a `ref` silently does nothing (no error, field just stays as-is) — a very quiet failure mode.

**Q: What's `refPath` for, and when would you need it?**
Used for **dynamic references** — when a field can point to different models depending on another field's value (polymorphic relations):
```js
const commentSchema = new Schema({
  commentableType: { type: String, enum: ['Post', 'Video'] },
  commentableId: { type: Schema.Types.ObjectId, refPath: 'commentableType' },
});
```

**Q: Does `.populate()` respect `.select('-password')` exclusions set at the schema level (`select: false`)?**
Yes, by default — schema-level `select: false` is honored during populate too, unless explicitly overridden with `.populate({ path: 'author', select: '+password' })`.

---

## Indexing Tricky Questions

**Q: You created an index but queries are still doing a collection scan (`COLLSCAN`). Why?**
Common causes: the query uses a field combination that doesn't match the index's field order (compound indexes are prefix-based — an index on `{a: 1, b: 1}` supports queries on `a` alone or `a+b`, but NOT `b` alone). Or a `$regex` without an anchor (`^`), a `$where`, or a query using `$ne`/`$nin` (these often can't use an index efficiently). Or the index simply hasn't finished building yet in the background.

**Q: What's the difference between a compound index `{a: 1, b: -1}` and two separate single-field indexes on `a` and `b`?**
A compound index can satisfy queries filtering/sorting on `a`, or on `a` + `b` together, in a single index scan. Two separate indexes generally can't be combined as efficiently for a query filtering on both fields simultaneously (MongoDB *can* do index intersection but it's usually much less efficient than one well-designed compound index).

**Q: Does a unique index allow multiple documents with the field missing entirely?**
Yes, by default — multiple documents can each *omit* the unique field, since `null`/missing is treated as one value only if the field is present as `null`. Actually — multiple documents with the field **completely absent** are all allowed since MongoDB doesn't consider "missing" the same as duplicate `null` values in the same way across versions; but multiple documents with the field **explicitly set to `null`** WILL collide on a standard unique index (only one `null` allowed) unless you use a **partial index** with `{ partialFilterExpression: { field: { $exists: true } } }` to only enforce uniqueness when the field is present.

**Q: Why might adding an index make writes slower — and is that always a bad trade?**
Every index must be updated on every insert/update/delete affecting the indexed field(s). More indexes = more write overhead. It's a legitimate trade-off: read-heavy collections benefit from more indexes, write-heavy collections (logs, events) should be indexed minimally and deliberately.

---

## Aggregation Edge Cases

**Q: Does `$match` behave differently placed at the start of a pipeline vs after a `$project`?**
Functionally it can be the same, but performance differs hugely. `$match` at the very start can use indexes (like a regular `find()`). Once you've passed through `$project`/`$group`/`$unwind`, subsequent `$match` stages operate on transformed, in-memory documents and can't use collection indexes anymore. **Always push `$match` as early as possible.**

**Q: What does `$unwind` do to a document where the array field is empty `[]` or missing?**
By default, `$unwind` **drops** documents where the array is empty or the field doesn't exist — they vanish from the pipeline output entirely. To keep them (with the field set to `null`), you must explicitly use:
```js
{ $unwind: { path: '$tags', preserveNullAndEmptyArrays: true } }
```
This is a very common silent-data-loss bug.

**Q: Can aggregation stages reference the results of earlier stages within the same `$group`?**
No — inside a single `$group` stage, all accumulator expressions (`$sum`, `$avg`, etc.) see the same input documents; you can't reference one accumulator's result from another accumulator in the same stage. You'd need a follow-up `$project` or `$addFields` stage after the `$group` to combine/derive from the grouped results.

**Q: Is `$lookup` equivalent to a SQL JOIN in terms of performance guarantees?**
Not quite — `$lookup` without an index on the foreign field results in a collection scan for every document being joined (huge cost at scale). Always ensure the `foreignField` in a `$lookup` is indexed, similar to how you'd index a foreign key column in SQL.

**Q: What's the default `maxTimeMS` behavior for aggregations, and why does that matter operationally?**
There's no default timeout unless you set one — a poorly written aggregation (e.g., missing early `$match`, unindexed `$lookup`) can run indefinitely and hold server resources. Senior engineers explicitly set `.option({ maxTimeMS: N })` on any aggregation exposed to user-triggered filters.

---

## Transactions & Concurrency

**Q: Do MongoDB transactions work on a standalone server, or only replica sets/sharded clusters?**
Multi-document transactions require a **replica set** (even a single-node replica set) or a sharded cluster — they do not work on a plain standalone `mongod` instance. This trips people up in local dev when they forgot to initialize their local Mongo as a replica set.

**Q: If a transaction fails partway through, does MongoDB auto-retry it?**
No, not by default at the application level — you're responsible for catching transient errors (labeled `TransientTransactionError` or `UnknownTransactionCommitResult`) and retrying the whole transaction block yourself. The driver doesn't silently retry your business logic for you.

**Q: What's a "dirty read" risk when NOT using transactions across multiple related writes?**
If you update two related documents without a transaction (e.g., debit one account, credit another) and the process crashes between the two writes, you get a permanently inconsistent state — no automatic rollback. Transactions exist specifically to make such multi-document operations atomic.

**Q: Are single-document updates ever non-atomic?**
No — single-document writes (including nested field updates, array operator updates) are **always atomic** in MongoDB, even without an explicit transaction. Transactions are only needed when atomicity must span **multiple documents/collections**.

---

## Data Modeling Trade-off Questions

**Q: "Would you embed comments inside a blog post document, or use a separate collection?" — what's the "right" answer?**
There isn't one universally right answer — that's the point of the question. Interviewers want you to reason about: expected comment volume (bounded vs unbounded — risk of hitting the 16MB document limit), whether comments are queried independently of posts (separate collection is better for that), write frequency (embedding causes contention if many comments arrive concurrently on one doc), and whether you need to paginate comments separately.

**Q: When would you deliberately denormalize/duplicate data across collections, and what's the cost?**
Denormalize (e.g., storing `authorName` directly on a `Post` alongside `authorId`) when read performance matters more than write complexity, and the duplicated field rarely changes. The cost: every time the source of truth changes (user renames themselves), you must update all denormalized copies — usually via a background job or event-driven update, which adds complexity and potential staleness windows.

**Q: How would you model a many-to-many relationship in MongoDB, given there's no native JOIN table concept?**
Two common approaches: (1) store an array of ObjectIds on one or both sides (e.g., `user.groupIds: [ObjectId]` and/or `group.memberIds: [ObjectId]`) and use `$in`/`populate` to resolve, or (2) create an explicit "join" collection (`memberships`) with `userId` + `groupId` + extra metadata (like `role` or `joinedAt`) when the relationship itself carries data — mirroring a traditional join table.

---

## Rapid-Fire "What Happens If…" Round

| Scenario | What Actually Happens |
|---|---|
| You `await` a query twice | Second await re-executes it (fresh DB round trip) — it is not cached |
| You pass an array to `findById` | Throws a `CastError` — expects a single ObjectId-like value |
| You forget `new Schema()` and just pass a plain object to `model()` | Mongoose auto-wraps it in a Schema in most setups, but you lose access to schema-specific chaining methods before model creation |
| Two processes `updateOne` the same doc simultaneously with `$inc` | Both increments apply — MongoDB serializes writes per document, no lost update |
| Two processes `findOne()` + `save()` the same doc simultaneously | Classic lost-update race condition — last `save()` wins, first change can be silently overwritten unless you use versioning (`optimisticConcurrency`) or atomic operators |
| You enable `optimisticConcurrency: true` on a schema | Mongoose adds a `__v` version check on every `save()` — concurrent saves against a stale version throw a `VersionError` instead of silently overwriting |
| You query with a field that has a typo (not in schema) under `strict: true` | The filter field is still sent as-is to MongoDB (strict mode affects writes/saves, not query filters) — so it silently matches nothing, no error |
| You call `.save()` on a document fetched with `.lean()` | Throws — plain objects from `.lean()` don't have `.save()`; it doesn't exist on them |
| Your app crashes mid `insertMany()` with `ordered: true` (default) | Insertion stops at the first failing document — earlier ones in the batch are already persisted, later ones are not attempted |
| You store a `Number` where the schema says `String` | Mongoose casts it automatically before saving (as long as it's cast-able) — no error, but can hide real bugs feeding wrong data |

---

## Bonus: Good Follow-Up Questions to Ask *Back* in an Interview

- "Is this a single-node deployment or a replica set / sharded cluster?" — changes answers around transactions and read-preference.
- "What's the expected document growth rate for this collection?" — changes embed-vs-reference answers.
- "Are we optimizing for read-heavy or write-heavy access patterns here?" — changes indexing strategy answers.

Asking these signals you think about trade-offs contextually rather than reciting memorized rules — which is usually exactly what a senior-level interview is probing for.