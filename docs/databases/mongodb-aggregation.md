# MongoDB Aggregation Pipeline & Advanced Topics

A deep-dive companion to the basic query guide — covers the aggregation framework in depth, plus advanced MongoDB/Mongoose topics: change streams, schema design at scale, geospatial queries, text search, and performance internals.

---

## Table of Contents

1. [Aggregation Pipeline Fundamentals](#aggregation-pipeline-fundamentals)
2. [Stage-by-Stage Deep Dive](#stage-by-stage-deep-dive)
3. [Aggregation Expressions & Operators](#aggregation-expressions--operators)
4. [$lookup Deep Dive (Joins)](#lookup-deep-dive-joins)
5. [$facet — Multiple Pipelines at Once](#facet--multiple-pipelines-at-once)
6. [Window Functions ($setWindowFields)](#window-functions-setwindowfields)
7. [Real-World Aggregation Recipes](#real-world-aggregation-recipes)
8. [Performance Tuning Aggregations](#performance-tuning-aggregations)
9. [Change Streams](#change-streams)
10. [Geospatial Queries](#geospatial-queries)
11. [Full-Text Search](#full-text-search)
12. [Schema Design at Scale](#schema-design-at-scale)
13. [Sharding Concepts](#sharding-concepts)
14. [Read/Write Concerns & Consistency](#readwrite-concerns--consistency)

---

## Aggregation Pipeline Fundamentals

The aggregation pipeline processes documents through a sequence of **stages**, where each stage transforms the documents and passes results to the next stage — similar to a Unix pipe.

```js
Model.aggregate([
  { stage1 },
  { stage2 },
  { stage3 },
]);
```

Key mental model: **each stage receives the output of the previous stage, not the original collection** (except the first stage). This is why field names can change mid-pipeline (`$group`'s output field is `_id`, not the original field name) and later stages must reference the *new* shape.

**Golden rule:** put `$match` (and `$sort` if it can use an index) as early as possible — earlier stages that reduce document count make every subsequent stage cheaper.

---

## Stage-by-Stage Deep Dive

### `$match`
Filters documents — same query syntax as `find()`. Can use indexes only when it's the **first** stage in the pipeline.
```js
{ $match: { status: 'active', createdAt: { $gte: startDate } } }
```

### `$project`
Reshapes documents — include/exclude/compute fields.
```js
{ $project: {
    name: 1,
    email: 1,
    isActive: { $eq: ['$status', 'active'] },
    _id: 0,
} }
```

### `$group`
Groups documents by an expression, applying accumulators.
```js
{ $group: {
    _id: '$department',
    totalSalary: { $sum: '$salary' },
    avgAge: { $avg: '$age' },
    employees: { $push: '$name' },        // collect into array
    uniqueRoles: { $addToSet: '$role' },   // collect distinct values
    count: { $sum: 1 },
    maxSalary: { $max: '$salary' },
    minSalary: { $min: '$salary' },
    first: { $first: '$name' },            // requires prior $sort
    last: { $last: '$name' },
} }
```
`_id: null` groups **all** documents into a single result (common for grand totals).

### `$sort`
```js
{ $sort: { totalSalary: -1 } }
```
Placed before `$group`, can leverage indexes. After `$group`, it's always an in-memory sort.

### `$limit` / `$skip`
```js
{ $limit: 10 }
{ $skip: 20 }
```
Order matters — `$sort` → `$skip` → `$limit` is the standard pagination pattern within a pipeline.

### `$unwind`
Deconstructs an array field into one document per element.
```js
{ $unwind: '$tags' }
// or with options:
{ $unwind: { path: '$tags', includeArrayIndex: 'tagIndex', preserveNullAndEmptyArrays: true } }
```
`preserveNullAndEmptyArrays: true` is essential when you don't want to silently drop documents with empty/missing arrays.

### `$addFields` (alias: no exclusion behavior, unlike `$project`)
Adds/overwrites fields **without** having to re-declare every field you want to keep — unlike `$project`, which requires listing everything you want in the output.
```js
{ $addFields: { fullName: { $concat: ['$firstName', ' ', '$lastName'] } } }
```

### `$count`
```js
{ $count: 'totalMatched' } // outputs a single doc: { totalMatched: N }
```

### `$replaceRoot` / `$replaceWith`
Promotes a nested/embedded document to be the top-level document.
```js
{ $replaceWith: '$address' } // output documents are now just the address objects
```

### `$bucket` / `$bucketAuto`
Groups documents into ranges (histograms).
```js
{ $bucket: {
    groupBy: '$age',
    boundaries: [0, 18, 30, 50, 100],
    default: 'Other',
    output: { count: { $sum: 1 } },
} }
```

### `$sortByCount`
Shortcut for grouping + counting + sorting descending in one stage.
```js
{ $sortByCount: '$category' }
// equivalent to: $group by category with $sum:1, then $sort by count desc
```

---

## Aggregation Expressions & Operators

Aggregation has its own expression language (used inside `$project`, `$group`, `$addFields`, etc.) — distinct from query operators.

### Arithmetic
```js
{ $sum: ['$a', '$b'] }
{ $subtract: ['$a', '$b'] }
{ $multiply: ['$a', '$b'] }
{ $divide: ['$a', '$b'] }
{ $round: ['$price', 2] }
```

### String
```js
{ $concat: ['$firstName', ' ', '$lastName'] }
{ $toUpper: '$name' }
{ $toLower: '$name' }
{ $substr: ['$name', 0, 3] }
{ $trim: { input: '$name' } }
{ $split: ['$tags', ','] }
```

### Conditional
```js
{ $cond: { if: { $gte: ['$age', 18] }, then: 'Adult', else: 'Minor' } }
{ $ifNull: ['$nickname', '$name'] } // fallback if field is null/missing
{ $switch: {
    branches: [
      { case: { $lt: ['$score', 50] }, then: 'Fail' },
      { case: { $lt: ['$score', 80] }, then: 'Pass' },
    ],
    default: 'Distinction',
} }
```

### Date
```js
{ $year: '$createdAt' }
{ $month: '$createdAt' }
{ $dayOfWeek: '$createdAt' }
{ $dateToString: { format: '%Y-%m-%d', date: '$createdAt' } }
{ $dateDiff: { startDate: '$start', endDate: '$end', unit: 'day' } }
{ $dateAdd: { startDate: '$createdAt', unit: 'day', amount: 7 } }
```

### Array
```js
{ $size: '$tags' }
{ $arrayElemAt: ['$tags', 0] }
{ $filter: { input: '$scores', as: 's', cond: { $gte: ['$$s', 50] } } }
{ $map: { input: '$scores', as: 's', in: { $multiply: ['$$s', 2] } } }
{ $reduce: { input: '$nums', initialValue: 0, in: { $add: ['$$value', '$$this'] } } }
{ $in: ['value', '$arrayField'] } // checks membership
```

### Type Conversion
```js
{ $toString: '$age' }
{ $toInt: '$stringField' }
{ $toDate: '$stringDate' }
{ $convert: { input: '$field', to: 'double', onError: 0, onNull: 0 } }
```

---

## $lookup Deep Dive (Joins)

### Basic (equality join)
```js
{ $lookup: {
    from: 'posts',
    localField: '_id',
    foreignField: 'authorId',
    as: 'posts',
} }
```
Result: array field `posts` added to each document (empty array if no match — never `null`).

### Advanced (pipeline-based $lookup — supports filtering, projection, multiple conditions)
```js
{ $lookup: {
    from: 'posts',
    let: { authorId: '$_id' },
    pipeline: [
      { $match: { $expr: { $eq: ['$authorId', '$$authorId'] } } },
      { $match: { published: true } },
      { $sort: { createdAt: -1 } },
      { $limit: 5 },
      { $project: { title: 1, createdAt: 1 } },
    ],
    as: 'recentPosts',
} }
```
This is the version to reach for whenever you need a join **plus** filtering/sorting/limiting on the joined side — the simple `localField`/`foreignField` form can't do that.

### Flattening a single-match lookup
```js
{ $lookup: { from: 'users', localField: 'authorId', foreignField: '_id', as: 'author' } },
{ $unwind: { path: '$author', preserveNullAndEmptyArrays: true } }, // array -> object
```

---

## $facet — Multiple Pipelines at Once

Runs several independent sub-pipelines against the **same input** in a single aggregation call — useful for building a search/listing endpoint that needs results + total count + filter facets in one round trip.

```js
await Product.aggregate([
  { $match: { category: 'electronics' } },
  { $facet: {
      data: [
        { $sort: { price: 1 } },
        { $skip: 0 },
        { $limit: 20 },
      ],
      totalCount: [
        { $count: 'count' },
      ],
      priceRanges: [
        { $bucket: { groupBy: '$price', boundaries: [0, 100, 500, 1000, 10000] } },
      ],
  } },
]);
// Output: { data: [...], totalCount: [{ count: N }], priceRanges: [...] }
```
Avoids running the `$match` filter multiple times for pagination + counting + faceting separately.

---

## Window Functions ($setWindowFields)

Available in MongoDB 5.0+. Lets you compute values across a "window" of related documents — running totals, moving averages, rankings — without collapsing rows the way `$group` does.

```js
await Sales.aggregate([
  { $setWindowFields: {
      partitionBy: '$region',
      sortBy: { date: 1 },
      output: {
        runningTotal: { $sum: '$amount', window: { documents: ['unbounded', 'current'] } },
        rank: { $rank: {} },
        movingAvg: { $avg: '$amount', window: { documents: [-2, 0] } }, // last 3 docs
      },
  } },
]);
```
This replaces what used to require awkward self-joins or application-level post-processing for things like leaderboard ranks or cumulative sums.

---

## Real-World Aggregation Recipes

### Paginated results + total count in one query (via `$facet`)
```js
const [result] = await Product.aggregate([
  { $match: { category: 'electronics' } },
  { $facet: {
      data: [{ $skip: (page - 1) * limit }, { $limit: limit }],
      total: [{ $count: 'count' }],
  } },
]);
const total = result.total[0]?.count || 0;
```

### Top N per group (e.g., top 3 highest-paid employees per department)
```js
await Employee.aggregate([
  { $sort: { department: 1, salary: -1 } },
  { $group: { _id: '$department', topEarners: { $push: { name: '$name', salary: '$salary' } } } },
  { $project: { topEarners: { $slice: ['$topEarners', 3] } } },
]);
```

### Find and remove exact duplicates, keeping one
```js
const duplicates = await User.aggregate([
  { $group: { _id: '$email', ids: { $push: '$_id' }, count: { $sum: 1 } } },
  { $match: { count: { $gt: 1 } } },
]);
for (const dup of duplicates) {
  const [keep, ...remove] = dup.ids;
  await User.deleteMany({ _id: { $in: remove } });
}
```

### Time-bucketed analytics (daily active users)
```js
await Session.aggregate([
  { $match: { createdAt: { $gte: startDate } } },
  { $group: {
      _id: { $dateToString: { format: '%Y-%m-%d', date: '$createdAt' } },
      uniqueUsers: { $addToSet: '$userId' },
  } },
  { $project: { date: '$_id', activeUsers: { $size: '$uniqueUsers' }, _id: 0 } },
  { $sort: { date: 1 } },
]);
```

### Conditional aggregation (count active vs inactive in one pass)
```js
await User.aggregate([
  { $group: {
      _id: null,
      active: { $sum: { $cond: [{ $eq: ['$status', 'active'] }, 1, 0] } },
      inactive: { $sum: { $cond: [{ $eq: ['$status', 'inactive'] }, 1, 0] } },
  } },
]);
```

---

## Performance Tuning Aggregations

- **Push `$match` and `$sort` as early as possible** — they can use indexes only at the very start of the pipeline (or right after another index-using stage in some cases).
- **Avoid `$project`/`$addFields` before a `$match`** that could otherwise use an index — reordering matters.
- **Index the `foreignField` of every `$lookup`** — an unindexed lookup degenerates into a per-document collection scan.
- **Use `.allowDiskUse(true)`** for large sorts/groups that exceed the 100MB in-memory limit per stage:
```js
await Model.aggregate([...]).allowDiskUse(true);
```
- **Set `maxTimeMS`** on any aggregation reachable from user input to prevent runaway queries from starving the server:
```js
await Model.aggregate([...]).option({ maxTimeMS: 5000 });
```
- **Use `$project` to drop unneeded fields early**, especially before `$group`/`$sort`, to reduce the amount of data flowing through later stages.
- **Profile with `.explain('executionStats')`** on the aggregate call, same as regular queries, to see whether `$lookup`/`$match` stages are using indexes (`IXSCAN`) or not (`COLLSCAN`).

---

## Change Streams

Watch a collection (or database, or whole cluster) for real-time changes — the modern replacement for manually polling. Requires a replica set or sharded cluster.

```js
const changeStream = User.watch([
  { $match: { operationType: { $in: ['insert', 'update', 'delete'] } } },
]);

changeStream.on('change', (change) => {
  console.log(change.operationType, change.documentKey);
  if (change.operationType === 'update') {
    console.log('Updated fields:', change.updateDescription.updatedFields);
  }
});

// Resume from a specific point after a restart (avoids missing events)
const resumeToken = getStoredResumeToken();
User.watch([], { resumeAfter: resumeToken });
```
Common use cases: cache invalidation, triggering webhooks/notifications, syncing to a search index (e.g., Elasticsearch) in near-real-time, audit logging.

---

## Geospatial Queries

Requires a `2dsphere` index for GeoJSON-based queries.

```js
const storeSchema = new Schema({
  name: String,
  location: {
    type: { type: String, enum: ['Point'], default: 'Point' },
    coordinates: { type: [Number] }, // [longitude, latitude] — order matters!
  },
});
storeSchema.index({ location: '2dsphere' });

// Find stores near a point, sorted by distance
await Store.find({
  location: {
    $near: {
      $geometry: { type: 'Point', coordinates: [77.209, 28.6139] },
      $maxDistance: 5000, // meters
    },
  },
});

// Find stores within a polygon (e.g., a delivery zone)
await Store.find({
  location: {
    $geoWithin: {
      $geometry: {
        type: 'Polygon',
        coordinates: [[[77.1, 28.6], [77.3, 28.6], [77.3, 28.7], [77.1, 28.7], [77.1, 28.6]]],
      },
    },
  },
});
```
Gotcha: GeoJSON coordinate order is **`[longitude, latitude]`**, the reverse of how most people naturally say "lat/long."

---

## Full-Text Search

```js
// Schema-level text index (can combine multiple fields, with weights)
articleSchema.index({ title: 'text', body: 'text' }, { weights: { title: 5, body: 1 } });

// Search
await Article.find({ $text: { $search: 'mongodb aggregation' } });

// Sort by relevance score
await Article.find(
  { $text: { $search: 'mongodb aggregation' } },
  { score: { $meta: 'textScore' } }
).sort({ score: { $meta: 'textScore' } });
```
Note: MongoDB's built-in `$text` search is basic (no fuzzy matching, limited relevance tuning) compared to dedicated search engines. For production-grade search (typo tolerance, faceting, ranking), most teams pair MongoDB with **Atlas Search** (built on Lucene) or an external engine like Elasticsearch/Algolia.

---

## Schema Design at Scale

**One Big Collection vs Many Small Collections**
Fewer, well-indexed collections with a clear access pattern usually beat over-normalized schemas — MongoDB rewards designing around your *query patterns*, not around strict normalization like relational DBs.

**Bucketing pattern** (for high-volume time-series-like data, e.g., IoT sensor readings):
```js
// Instead of one document per reading (millions of tiny docs):
{
  sensorId: 'abc',
  hour: ISODate('2026-08-15T10:00:00Z'),
  readings: [
    { ts: ISODate(...), value: 22.5 },
    { ts: ISODate(...), value: 22.7 },
    // up to N readings per bucket document
  ],
  count: 2,
}
```
Reduces document count and index overhead dramatically versus one document per data point. MongoDB 5.0+ also has native **time series collections** (`db.createCollection('readings', { timeseries: { timeField: 'ts', metaField: 'sensorId' } })`) that do this automatically.

**Outlier pattern** — when 99% of documents have a small embedded array but a rare few would grow unbounded (e.g., a viral post with millions of likes): keep the array embedded for the common case, but detect and "overflow" into a linked collection once a threshold is crossed, flagging the parent doc (`hasOverflow: true`).

**Extended reference pattern** — instead of a full `populate()` on every read, duplicate a few frequently-needed fields (e.g., `authorName`, `authorAvatar`) directly onto the referencing document, avoiding a join for the common display case while keeping the full reference for when complete data is needed.

---

## Sharding Concepts

(Conceptual — most teams don't manage sharding directly day-to-day, but understanding it matters for interviews and scaling decisions.)

- **Shard key** choice is the single most important decision — it determines how data distributes across shards. A poor shard key (e.g., a monotonically increasing field like `createdAt` or auto-incrementing IDs) creates "hot shards" where all new writes land on one shard.
- **Good shard keys** have high cardinality, even distribution, and ideally align with common query patterns (queries that don't include the shard key must "scatter-gather" across all shards, which is slow).
- **Compound shard keys** (e.g., `{ region: 1, userId: 1 }`) are common to balance distribution while keeping related data grouped.
- Once chosen, changing a shard key historically required significant migration effort (newer MongoDB versions have added shard key refinement capabilities, but it's still not trivial).

---

## Read/Write Concerns & Consistency

**Write concern** — how many nodes must acknowledge a write before it's considered successful:
```js
await User.create([{ name: 'A' }], { writeConcern: { w: 'majority', wtimeout: 5000 } });
```
`w: 1` (default-ish) = fast but risks losing the write if the primary fails before replication. `w: 'majority'` = safer, waits for replica acknowledgment, slightly slower.

**Read concern** — what guarantees a read has about the data's durability/consistency:
```js
await User.find().read('secondary'); // read preference: allow reads from replicas
```
`readConcern: 'majority'` ensures you only read data that's been replicated to a majority of nodes (won't see data that could later be rolled back). Useful in financial/critical-consistency contexts.

**Causal consistency** — within a single client session, you can guarantee that reads reflect all previous writes from that same session, even across a replica set, using `session`-scoped operations. Important for "read your own write" scenarios in distributed reads.

---

## Quick Reference: When to Reach for What

| Need | Tool |
|---|---|
| Simple filter + sort + paginate | `find()` chain |
| Join + group + reshape | Aggregation pipeline |
| Join with filtering on the joined side | `$lookup` with `pipeline` |
| Results + count + facets in one call | `$facet` |
| Running totals / rankings | `$setWindowFields` |
| Real-time change notifications | Change Streams |
| "Near me" queries | `2dsphere` index + `$near` |
| Keyword search | `$text` index (or Atlas Search for production-grade) |
| High-volume time-series data | Time series collections / bucketing pattern |
| Multi-document atomic writes | Transactions (needs replica set) |