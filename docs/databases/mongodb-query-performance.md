# MongoDB / Mongoose: Query Performance & Debugging Guide

A focused, practical guide to writing efficient queries and systematically finding/fixing performance bottlenecks — from indexing fundamentals to reading `explain()` output to diagnosing real production slowdowns.

---

## Table of Contents

1. [Mental Model: Why Queries Are Slow](#mental-model-why-queries-are-slow)
2. [Indexing Fundamentals](#indexing-fundamentals)
3. [Compound Index Strategy (ESR Rule)](#compound-index-strategy-esr-rule)
4. [Reading `.explain()` Output](#reading-explain-output)
5. [The Query Profiler](#the-query-profiler)
6. [Common Performance Killers](#common-performance-killers)
7. [Fixing N+1 Queries](#fixing-n1-queries)
8. [Pagination Done Right](#pagination-done-right)
9. [Aggregation-Specific Performance](#aggregation-specific-performance)
10. [Connection & Pooling Bottlenecks](#connection--pooling-bottlenecks)
11. [Write Performance](#write-performance)
12. [Schema-Level Performance Decisions](#schema-level-performance-decisions)
13. [Monitoring & Ongoing Detection](#monitoring--ongoing-detection)
14. [Step-by-Step Debugging Workflow](#step-by-step-debugging-workflow)
15. [Performance Checklist](#performance-checklist)

---

## Mental Model: Why Queries Are Slow

Almost every MongoDB performance problem traces back to one of these:

1. **No usable index** → server scans every document (`COLLSCAN`) instead of jumping directly to matches (`IXSCAN`).
2. **Wrong index shape** → an index exists, but doesn't match the query's filter/sort combination, so it's ignored or only partially used.
3. **Too much data returned/transferred** → fetching full documents or huge result sets when only a few fields/rows were needed.
4. **Too many round trips** → N+1 query patterns, unbatched writes, sequential awaits that could be parallel.
5. **In-memory operations exceeding limits** → large sorts/groups without indexes triggering disk spills or 32MB sort limit errors.
6. **Lock contention / write pressure** → many concurrent writes to the same document or hot shard key.
7. **Unbounded growth** → documents or arrays growing over time without a strategy, degrading every read that touches them.

Every debugging session should start by figuring out **which of these 7** is actually happening — guessing and randomly adding indexes wastes time.

---

## Indexing Fundamentals

**An index lets MongoDB avoid scanning the whole collection** by maintaining a sorted structure (B-tree) on the indexed field(s), so it can binary-search directly to matching documents.

```js
// Single field index
userSchema.index({ email: 1 }); // 1 = ascending, -1 = descending

// Compound index
userSchema.index({ role: 1, createdAt: -1 });

// Unique index
userSchema.index({ email: 1 }, { unique: true });

// Partial index — only indexes documents matching a condition (smaller, cheaper)
userSchema.index(
  { email: 1 },
  { unique: true, partialFilterExpression: { email: { $exists: true } } }
);

// TTL index — auto-delete documents after a time period
sessionSchema.index({ createdAt: 1 }, { expireAfterSeconds: 3600 });

// Sparse index — skips documents missing the field entirely
userSchema.index({ referralCode: 1 }, { sparse: true });
```

**Every index has a cost**: disk space, memory (ideally the working set of indexes fits in RAM), and slower writes (every insert/update must also update every relevant index). Index what you actually query/sort/filter on — not everything defensively.

**Rule of thumb**: if a field appears in a `find()` filter, a `sort()`, or a `$lookup`'s `foreignField` — it's an index candidate. If it's rarely queried directly, it probably isn't.

---

## Compound Index Strategy (ESR Rule)

When building a compound index, order fields as: **Equality → Sort → Range** (ESR).

```js
// Query pattern:
User.find({ status: 'active', role: 'admin' })  // equality fields
    .sort({ createdAt: -1 })                     // sort field
    .where({ age: { $gte: 18 } });                // range field

// Ideal index:
userSchema.index({ status: 1, role: 1, createdAt: -1, age: 1 });
```

**Why this order matters:**
- **Equality fields first** — MongoDB can narrow down to an exact "slice" of the index immediately.
- **Sort field next** — if it's in the right position, MongoDB can read results *already in sorted order* straight from the index, avoiding an expensive in-memory sort.
- **Range fields last** — range conditions (`$gt`, `$lt`, `$in` with many values) scan a contiguous chunk of the index, so anything after a range field in the index becomes far less useful and often ignored.

**Prefix rule**: a compound index on `{a: 1, b: 1, c: 1}` can serve queries filtering on `a` alone, `a+b`, or `a+b+c` — but **not** `b` alone or `c` alone. Field order is not interchangeable.

```js
// This index...
userSchema.index({ status: 1, createdAt: -1 });

// ...serves these queries efficiently:
User.find({ status: 'active' });
User.find({ status: 'active' }).sort({ createdAt: -1 });

// ...but NOT this one efficiently (status isn't in the filter):
User.find().sort({ createdAt: -1 }); // needs a separate index on createdAt alone
```

---

## Reading `.explain()` Output

This is the single most important debugging tool. Always start here before guessing.

```js
const plan = await User.find({ status: 'active' })
  .sort({ createdAt: -1 })
  .explain('executionStats');

console.log(JSON.stringify(plan, null, 2));
```

**What to look for:**

| Field | What it tells you |
|---|---|
| `winningPlan.stage` | `IXSCAN` = good, using an index. `COLLSCAN` = bad, scanning the whole collection. `SORT` (as its own stage, not merged into IXSCAN) = in-memory sort happening — expensive. |
| `executionStats.nReturned` | How many documents actually matched/returned |
| `executionStats.totalDocsExamined` | How many documents MongoDB had to look at |
| `executionStats.totalKeysExamined` | How many index entries were scanned |
| `executionStats.executionTimeMillis` | Real time taken |

**The #1 red flag**: `totalDocsExamined` much larger than `nReturned`. If you examined 500,000 documents to return 10, that's a missing or ineffective index — full stop.

**Healthy example** (index working well):
```
nReturned: 10
totalDocsExamined: 10
totalKeysExamined: 10
stage: "IXSCAN"
```

**Unhealthy example** (index missing or wrong shape):
```
nReturned: 10
totalDocsExamined: 480000
stage: "COLLSCAN"
```

**Also check**: if `stage` shows `IXSCAN` immediately followed by a separate `SORT` stage in the plan tree, it means the index handled filtering but **not** the sort — the sort happened in memory afterward. Fix by extending the index to also cover the sort field in the correct position (see ESR rule above).

---

## The Query Profiler

For catching slow queries you didn't know to look for (as opposed to `.explain()` which requires you to already suspect a specific query).

```js
// Enable on a dev/staging DB — log any query slower than 100ms
db.setProfilingLevel(1, { slowms: 100 });

// Review recent slow queries
db.system.profile.find().sort({ ts: -1 }).limit(20).pretty();

// Find the slowest queries in the last hour
db.system.profile.find({
  ts: { $gte: new Date(Date.now() - 3600000) }
}).sort({ millis: -1 }).limit(10);

// Turn off when done (profiling has its own overhead — don't leave it on in prod at level 2)
db.setProfilingLevel(0);
```

**Profiling levels**: `0` = off, `1` = log slow queries only (`slowms` threshold), `2` = log **every** query (very high overhead — dev/debugging only, never leave on in production).

**In application code**, enable Mongoose's debug mode during development to see every query as it happens:
```js
mongoose.set('debug', true);
```

---

## Common Performance Killers

### 1. Missing or wrong-shaped index
Covered above — the most common cause by far. Check with `.explain()`.

### 2. Regex without an anchor
```js
// Bad — can't use an index efficiently, scans every value
User.find({ name: { $regex: 'ohn', $options: 'i' } });

// Better — anchored prefix regex CAN use an index
User.find({ name: { $regex: '^John', $options: 'i' } });
```
Unanchored regex (searching for a substring anywhere in the string) fundamentally can't binary-search an index — it has to check every entry. If you need real substring/fuzzy search at scale, use a text index or a dedicated search engine (Atlas Search, Elasticsearch).

### 3. `$ne` / `$nin` / `$exists: false`
These are inherently inefficient — they describe "everything except X," which doesn't narrow down an index range well. If used often, consider restructuring the query or adding a computed boolean field you can index instead (e.g., `isActive: false` instead of `status: { $ne: 'active' }`).

### 4. Sorting without a supporting index
Any `.sort()` not backed by an index (or not in the right position in a compound index) is done **in memory**, and MongoDB has a 100MB limit for this per pipeline stage before it either errors out or requires `.allowDiskUse(true)` — which is dramatically slower than an index-backed sort.

### 5. Fetching full documents when you need a few fields
```js
// Wasteful
const users = await User.find({ role: 'admin' });

// Efficient
const users = await User.find({ role: 'admin' }).select('name email').lean();
```
Reduces both server-side work and network transfer.

### 6. `skip()` on large offsets (deep pagination)
```js
// Gets progressively slower as page number grows — MongoDB still has to walk past all skipped docs
User.find().sort({ createdAt: -1 }).skip(100000).limit(20);
```
See [Pagination Done Right](#pagination-done-right) for the fix.

### 7. Large `$in` arrays
```js
User.find({ _id: { $in: [/* 50,000 ids */] } });
```
Very large `$in` lists can degrade performance and even hit BSON size limits. Batch them (e.g., chunks of a few thousand) if the array is generated dynamically and can grow unbounded.

### 8. Unindexed `$lookup` foreign field
Covered in the aggregation guide — always index the `foreignField` side of a `$lookup`, or it collapses into a per-document collection scan.

### 9. Growing arrays inside a single document
Every update to a document with a large embedded array (especially with `$push`) becomes more expensive as the array grows, and it also bloats every read that touches the document, even if the array field isn't needed. Watch for documents approaching the 16MB limit — it's usually a modeling symptom, not just a performance one.

### 10. Case-insensitive queries without a collation index
```js
// Without a proper index, this forces a full scan even on an indexed field:
User.find({ email: 'ALICE@MAIL.COM' }); // won't match 'alice@mail.com' + can't use a case-sensitive index efficiently for case-insensitive matching

// Fix: normalize on write (store lowercase) AND query lowercase
userSchema.pre('save', function (next) {
  this.email = this.email.toLowerCase();
  next();
});
// or use a collation-aware index if case-insensitive search on mixed-case data is required
userSchema.index({ email: 1 }, { collation: { locale: 'en', strength: 2 } });
```

---

## Fixing N+1 Queries

The classic ORM-style trap, just as relevant in Mongoose.

```js
// BAD: 1 query for posts + N queries for authors (N+1)
const posts = await Post.find();
for (const post of posts) {
  post.author = await User.findById(post.authorId); // one query per post!
}
```

**Fix 1 — batch with `$in`:**
```js
const posts = await Post.find().lean();
const authorIds = [...new Set(posts.map(p => p.authorId.toString()))];
const authors = await User.find({ _id: { $in: authorIds } }).lean();
const authorMap = new Map(authors.map(a => [a._id.toString(), a]));
posts.forEach(p => { p.author = authorMap.get(p.authorId.toString()); });
```

**Fix 2 — use `populate()`** (Mongoose does the batching for you internally):
```js
const posts = await Post.find().populate('author').lean();
```

**Fix 3 — use `$lookup`** in an aggregation when you also need filtering/grouping on the joined data in the same round trip.

---

## Pagination Done Right

**Offset pagination (`skip`/`limit`) degrades on deep pages** — MongoDB must walk through and discard every skipped document.

```js
// Gets slower as `page` grows
User.find().sort({ _id: 1 }).skip((page - 1) * limit).limit(limit);
```

**Cursor-based (keyset) pagination is far more efficient at scale** — use the last seen value as the filter instead of counting offsets:
```js
// First page
let results = await User.find().sort({ _id: 1 }).limit(20);

// Next page — filter using the last _id from the previous page, no skip needed
const lastId = results[results.length - 1]._id;
results = await User.find({ _id: { $gt: lastId } }).sort({ _id: 1 }).limit(20);
```
This is a constant-time lookup at any depth (as long as the sort field is indexed), versus offset pagination which gets linearly worse. Trade-off: you lose the ability to jump to an arbitrary page number directly — it's "next/previous" style pagination, which fits infinite-scroll UIs well but not numbered page links.

---

## Aggregation-Specific Performance

(Expanded detail in the dedicated aggregation guide — summarized here for completeness.)

- Put `$match` first — only stage that can use an index at pipeline start.
- Index `$lookup`'s `foreignField`.
- Use `.allowDiskUse(true)` for large sorts/groups, but treat it as a fallback, not a fix — better to reduce data volume earlier in the pipeline.
- Set `.option({ maxTimeMS: N })` on any user-triggered aggregation.
- Use `$project` early to drop unneeded fields before heavy stages like `$group`/`$sort`.
- Profile with `.explain('executionStats')` on the aggregate call itself — same red flags apply (`COLLSCAN` vs `IXSCAN`).

---

## Connection & Pooling Bottlenecks

Sometimes the query itself is fine, but the app is slow because it's starved for connections.

```js
mongoose.connect(uri, {
  maxPoolSize: 100,      // default is 100 in recent driver versions — tune based on concurrency needs
  minPoolSize: 10,       // keep some warm connections ready
  socketTimeoutMS: 45000,
  serverSelectionTimeoutMS: 5000,
});
```

**Symptoms of pool exhaustion**: requests queueing/timing out under load even though individual query times look fine in isolation. Check `maxPoolSize` against your expected concurrent request volume — a pool too small serializes what should be parallel work.

**Also watch for**: accidentally creating a new Mongoose connection per request instead of reusing a single shared connection — a very common bug in serverless/lambda environments where connection setup isn't cached across invocations.

---

## Write Performance

- **Batch writes** with `bulkWrite`/`insertMany` instead of looping individual `save()`/`updateOne()` calls — each individual call is a full round trip.
- **Every index slows writes** slightly — don't over-index write-heavy collections (logs, events, high-frequency sensor data).
- **Avoid updating fields you don't need to** — a `$set` on unchanged data still triggers index maintenance and write-lock overhead for no benefit; diff before writing if updates are frequent and mostly no-ops.
- **Use `ordered: false`** on bulk inserts if you don't need strict ordering and want failures in one document to not block the rest:
```js
await User.insertMany(bigArray, { ordered: false });
```
- **Write concern trade-off**: `w: 'majority'` is safer but slower per write; `w: 1` is faster but can lose the acknowledgment guarantee if the primary fails immediately after. Choose deliberately per use case, not globally.

---

## Schema-Level Performance Decisions

- **Avoid unbounded arrays** in a single document — they degrade both read and write performance as they grow, and eventually risk the 16MB document limit. Move to a separate collection with a reference once growth is unbounded.
- **Denormalize a few frequently-read fields** (e.g., `authorName` on a `Post`) to avoid `populate()`/`$lookup` on every read of a hot path, if the source data changes rarely.
- **Use TTL indexes** to auto-expire time-limited data (sessions, temporary tokens, logs) instead of relying on a cron job to clean up — reduces both storage bloat and the size of collections you're querying against.
- **Consider bucketing pattern or native time series collections** for high-frequency data (IoT, metrics, logs) instead of one document per data point — see the aggregation/advanced doc for details.

---

## Monitoring & Ongoing Detection

Don't just debug reactively — set up ongoing visibility:

- **MongoDB Atlas Performance Advisor** (if using Atlas) — automatically suggests missing indexes based on real query patterns.
- **`db.currentOp()`** — see what's running right now, useful for catching a query that's hanging in production:
```js
db.currentOp({ "secs_running": { "$gt": 5 } }); // ops running longer than 5s
```
- **`db.collection.stats()`** — check index sizes, document counts, storage size to catch bloat before it becomes a crisis.
- **APM tools** (Datadog, New Relic, Atlas built-in monitoring) — track query latency percentiles (p50/p95/p99) over time, not just averages, since averages hide the worst-case tail that actually hurts users.
- **Alert on slow query log growth** — a sudden spike in `system.profile` entries is often the earliest signal of a new bottleneck (e.g., after a schema change or new feature ships).

---

## Step-by-Step Debugging Workflow

When something is slow in production, work through this in order:

1. **Identify the actual slow operation.** Use APM traces, slow query logs, or `db.currentOp()` — don't guess which query is the problem.
2. **Run `.explain('executionStats')` on that exact query** with realistic filter values (not a toy example — real skew in data can behave very differently).
3. **Check `totalDocsExamined` vs `nReturned`.** Big gap → indexing problem. Go to step 4. Close match but still slow → likely a data volume / network / application logic problem, not indexing. Go to step 6.
4. **Check whether an index exists that matches the filter+sort fields, in the right order (ESR rule).** If not, design one and test again with `.explain()` before deploying — verify the fix.
5. **Check if the query is doing an in-memory sort** (separate `SORT` stage after `IXSCAN`). Extend the index to cover the sort field in the correct position.
6. **If indexing looks fine, check the payload.** Are you fetching whole documents when you need 3 fields? Add `.select()` + `.lean()`.
7. **Check for N+1 patterns** in the surrounding application code — is this "slow query" actually hundreds of fast queries in a loop?
8. **Check connection pool metrics** — is the query itself fast, but requests are queueing for a free connection?
9. **Re-run `.explain()` after each change** to confirm the fix actually worked — don't assume; verify with `totalDocsExamined`/`executionTimeMillis` numbers before and after.
10. **Add the fix to your indexing/schema documentation** so the next person (or you, in six months) doesn't have to rediscover it.

---

## Performance Checklist

- [ ] Every field used in a `find()` filter, `.sort()`, or `$lookup` foreignField has a supporting index.
- [ ] Compound indexes follow the ESR rule (Equality → Sort → Range).
- [ ] `.explain('executionStats')` shows `IXSCAN`, not `COLLSCAN`, on hot-path queries.
- [ ] `totalDocsExamined` is close to `nReturned` on frequently-run queries.
- [ ] Read-only queries use `.lean()` and `.select()` to avoid over-fetching.
- [ ] Deep pagination uses cursor-based pagination, not large `skip()` offsets.
- [ ] No N+1 query loops — batched with `$in`, `populate()`, or `$lookup`.
- [ ] Aggregations start with `$match`, index the `$lookup` side, and have `maxTimeMS` set for user-triggered pipelines.
- [ ] Bulk writes use `bulkWrite`/`insertMany` instead of looped individual writes.
- [ ] Connection pool size (`maxPoolSize`) matches real concurrency needs.
- [ ] No unbounded arrays growing inside single documents.
- [ ] Slow query monitoring/alerting exists so regressions are caught before users report them.