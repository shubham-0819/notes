# Mongoose / MongoDB Query Guide

A practical reference for the most commonly used Mongoose and MongoDB commands — CRUD, query operators, update operators, aggregation, population, indexing, and more.

---

## Table of Contents

1. [Setup & Connection](#setup--connection)
2. [Schema & Model Basics](#schema--model-basics)
3. [Create](#create)
4. [Read / Find](#read--find)
5. [Query Operators](#query-operators)
6. [Update](#update)
7. [Update Operators](#update-operators)
8. [Delete](#delete)
9. [Sorting, Limiting, Pagination](#sorting-limiting-pagination)
10. [Projection (Selecting Fields)](#projection-selecting-fields)
11. [Population (Joins)](#population-joins)
12. [Aggregation Pipeline](#aggregation-pipeline)
13. [Indexes](#indexes)
14. [Transactions](#transactions)
15. [Validation & Middleware](#validation--middleware)
16. [Useful Instance/Model Methods](#useful-instancemodel-methods)
17. [Native MongoDB Shell Equivalents](#native-mongodb-shell-equivalents)

---

## Setup & Connection

```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/mydb', {
  // options are mostly automatic in Mongoose 6+/7+
});

mongoose.connection.on('connected', () => console.log('DB connected'));
mongoose.connection.on('error', (err) => console.error(err));
```

---

## Schema & Model Basics

```js
const { Schema, model } = mongoose;

const userSchema = new Schema({
  name: { type: String, required: true, trim: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  age: { type: Number, min: 0, max: 120 },
  role: { type: String, enum: ['user', 'admin'], default: 'user' },
  tags: [String],
  address: {
    city: String,
    zip: String,
  },
  createdAt: { type: Date, default: Date.now },
}, { timestamps: true }); // adds createdAt & updatedAt automatically

const User = model('User', userSchema);
```

---

## Create

```js
// Single document
const user = await User.create({ name: 'Alice', email: 'alice@mail.com' });

// Multiple documents
const users = await User.insertMany([
  { name: 'Bob', email: 'bob@mail.com' },
  { name: 'Carl', email: 'carl@mail.com' },
]);

// Instantiate + save (useful when you need pre-save hooks/validation before persisting)
const newUser = new User({ name: 'Dana', email: 'dana@mail.com' });
await newUser.save();
```

---

## Read / Find

```js
// Find all
const all = await User.find();

// Find with filter
const admins = await User.find({ role: 'admin' });

// Find one
const one = await User.findOne({ email: 'alice@mail.com' });

// Find by ID
const byId = await User.findById('64f1a2b3c4d5e6f7a8b9c0d1');

// Count documents
const count = await User.countDocuments({ role: 'user' });

// Check existence (lightweight, returns _id or null)
const exists = await User.exists({ email: 'alice@mail.com' });

// Distinct values
const cities = await User.distinct('address.city');
```

---

## Query Operators

Used inside filter objects passed to `find`, `findOne`, `updateMany`, etc.

### Comparison
```js
User.find({ age: { $eq: 25 } });        // equal
User.find({ age: { $ne: 25 } });        // not equal
User.find({ age: { $gt: 18 } });        // greater than
User.find({ age: { $gte: 18 } });       // greater than or equal
User.find({ age: { $lt: 65 } });        // less than
User.find({ age: { $lte: 65 } });       // less than or equal
User.find({ age: { $in: [20, 25, 30] } });   // matches any in array
User.find({ age: { $nin: [20, 25, 30] } });  // matches none in array
```

### Logical
```js
User.find({ $and: [{ age: { $gte: 18 } }, { role: 'admin' }] });
User.find({ $or: [{ role: 'admin' }, { age: { $gt: 60 } }] });
User.find({ $nor: [{ role: 'guest' }] });
User.find({ age: { $not: { $lt: 18 } } });
```

### Element
```js
User.find({ age: { $exists: true } });
User.find({ age: { $type: 'number' } });
```

### Evaluation
```js
User.find({ name: { $regex: /^A/, $options: 'i' } }); // starts with "A", case-insensitive
User.find({ $text: { $search: 'developer' } });        // requires text index
User.find({ $expr: { $gt: ['$spent', '$budget'] } });  // compare two fields
User.find({ age: { $mod: [2, 0] } });                  // even ages
```

### Array
```js
User.find({ tags: { $all: ['js', 'node'] } });     // contains all values
User.find({ tags: { $size: 3 } });                 // array length equals 3
User.find({ tags: { $elemMatch: { $eq: 'admin' } } });
User.find({ 'scores': { $elemMatch: { $gte: 80, $lt: 90 } } });
```

### Nested / Dot Notation
```js
User.find({ 'address.city': 'Delhi' });
```

---

## Update

```js
// Update one document, returns write result by default
await User.updateOne({ _id: id }, { $set: { name: 'Alice2' } });

// Update many
await User.updateMany({ role: 'user' }, { $set: { active: true } });

// Find and update, returns the document (old or new)
const updated = await User.findOneAndUpdate(
  { email: 'alice@mail.com' },
  { $set: { name: 'Alice Updated' } },
  { new: true, runValidators: true } // new: return updated doc; runValidators: enforce schema rules
);

// Update by ID
await User.findByIdAndUpdate(id, { $set: { age: 30 } }, { new: true });

// Upsert (insert if not found)
await User.updateOne(
  { email: 'new@mail.com' },
  { $set: { name: 'New User' } },
  { upsert: true }
);

// Replace entire document (except _id)
await User.replaceOne({ _id: id }, { name: 'Fresh', email: 'fresh@mail.com' });
```

---

## Update Operators

```js
// Field operators
{ $set: { name: 'New Name' } }             // set field value
{ $unset: { age: '' } }                    // remove field
{ $inc: { age: 1 } }                       // increment/decrement
{ $mul: { price: 1.1 } }                   // multiply
{ $rename: { oldField: 'newField' } }      // rename field
{ $min: { age: 18 } }                      // set only if new value is lower
{ $max: { age: 65 } }                      // set only if new value is higher
{ $currentDate: { updatedAt: true } }      // set to current date

// Array operators
{ $push: { tags: 'newTag' } }                          // add to array
{ $push: { tags: { $each: ['a', 'b'] } } }              // add multiple
{ $addToSet: { tags: 'uniqueTag' } }                    // add if not present
{ $pop: { tags: 1 } }                                   // remove last (1) or first (-1)
{ $pull: { tags: 'oldTag' } }                           // remove matching value(s)
{ $pullAll: { tags: ['a', 'b'] } }                      // remove all listed values
{ $[]: {} }                                             // update all array elements (positional all)

// Positional operators
await User.updateOne(
  { _id: id, 'scores.subject': 'math' },
  { $set: { 'scores.$.grade': 'A' } }   // update first matching array element
);

await User.updateOne(
  { _id: id },
  { $set: { 'scores.$[elem].grade': 'B' } },
  { arrayFilters: [{ 'elem.subject': 'science' }] } // update all matching filtered elements
);
```

---

## Delete

```js
await User.deleteOne({ email: 'alice@mail.com' });
await User.deleteMany({ role: 'guest' });
await User.findByIdAndDelete(id);
await User.findOneAndDelete({ email: 'bob@mail.com' });
```

---

## Sorting, Limiting, Pagination

```js
// Sort ascending (1) / descending (-1)
await User.find().sort({ createdAt: -1 });

// Limit & skip (pagination)
const page = 2, limit = 10;
await User.find()
  .sort({ createdAt: -1 })
  .skip((page - 1) * limit)
  .limit(limit);

// Chaining a full query
const results = await User.find({ role: 'user' })
  .select('name email')
  .sort('-createdAt')
  .limit(20)
  .lean(); // returns plain JS objects, faster, no Mongoose document overhead
```

---

## Projection (Selecting Fields)

```js
// Include only name and email
await User.find({}, 'name email');
await User.find().select('name email');

// Exclude fields
await User.find().select('-password -__v');

// Object syntax
await User.find({}, { name: 1, email: 1, _id: 0 });
```

---

## Population (Joins)

```js
const postSchema = new Schema({
  title: String,
  author: { type: Schema.Types.ObjectId, ref: 'User' },
});
const Post = model('Post', postSchema);

// Basic populate
const posts = await Post.find().populate('author');

// Populate specific fields only
await Post.find().populate('author', 'name email');

// Nested populate
await Post.find().populate({
  path: 'author',
  populate: { path: 'company', select: 'name' },
});

// Populate with query conditions
await Post.find().populate({
  path: 'author',
  match: { role: 'admin' },
});

// Multiple populate calls
await Post.find().populate('author').populate('comments');
```

---

## Aggregation Pipeline

```js
const result = await User.aggregate([
  { $match: { role: 'user' } },                     // filter
  { $group: {                                        // group + accumulate
      _id: '$address.city',
      total: { $sum: 1 },
      avgAge: { $avg: '$age' },
    } },
  { $sort: { total: -1 } },                          // sort
  { $limit: 10 },
  { $project: { city: '$_id', total: 1, avgAge: 1, _id: 0 } }, // reshape output
]);

// Common stages
{ $match: {} }        // filter documents
{ $group: {} }        // group and aggregate ($sum, $avg, $min, $max, $push, $addToSet)
{ $sort: {} }         // sort results
{ $limit: 10 }        // limit results
{ $skip: 10 }         // skip results
{ $project: {} }      // reshape/select fields
{ $unwind: '$tags' }  // flatten array field into separate documents
{ $lookup: {          // join with another collection
    from: 'posts',
    localField: '_id',
    foreignField: 'author',
    as: 'posts',
  } }
{ $addFields: {} }    // add computed fields
{ $count: 'total' }   // count matching documents
{ $facet: {} }        // run multiple sub-pipelines in parallel
```

---

## Indexes

```js
// In schema
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ name: 'text' }); // text index for $text search
userSchema.index({ location: '2dsphere' }); // geospatial index

// Programmatically
await User.collection.createIndex({ age: 1 });

// List indexes
await User.collection.getIndexes();

// Drop index
await User.collection.dropIndex('email_1');
```

---

## Transactions

```js
const session = await mongoose.startSession();
session.startTransaction();

try {
  await User.updateOne({ _id: id1 }, { $inc: { balance: -100 } }, { session });
  await User.updateOne({ _id: id2 }, { $inc: { balance: 100 } }, { session });
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
  throw err;
} finally {
  session.endSession();
}
```

---

## Validation & Middleware

```js
// Custom validator
userSchema.path('email').validate((val) => /\S+@\S+\.\S+/.test(val), 'Invalid email');

// Pre/post hooks
userSchema.pre('save', function (next) {
  this.email = this.email.toLowerCase();
  next();
});

userSchema.post('save', function (doc) {
  console.log(`User saved: ${doc._id}`);
});

// Query middleware (e.g., soft delete filtering)
userSchema.pre(/^find/, function (next) {
  this.where({ deleted: { $ne: true } });
  next();
});
```

---

## Useful Instance/Model Methods

```js
// Instance methods
userSchema.methods.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
};
user.getFullName();

// Static (model-level) methods
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};
User.findByEmail('alice@mail.com');

// Virtuals (computed, not stored)
userSchema.virtual('isAdult').get(function () {
  return this.age >= 18;
});

// toJSON/toObject transform (e.g., hide fields in API responses)
userSchema.set('toJSON', {
  transform: (doc, ret) => {
    delete ret.password;
    return ret;
  },
});
```

---

## Native MongoDB Shell Equivalents

For quick reference when working in `mongosh` directly instead of Mongoose:

```js
db.users.find({ role: "admin" })
db.users.findOne({ email: "alice@mail.com" })
db.users.insertOne({ name: "Alice" })
db.users.insertMany([{ name: "Bob" }, { name: "Carl" }])
db.users.updateOne({ _id: id }, { $set: { name: "New" } })
db.users.updateMany({ role: "user" }, { $set: { active: true } })
db.users.deleteOne({ _id: id })
db.users.deleteMany({ role: "guest" })
db.users.countDocuments({ role: "user" })
db.users.aggregate([{ $match: { role: "user" } }])
db.users.createIndex({ email: 1 }, { unique: true })
db.users.explain("executionStats").find({ role: "admin" }) // query performance analysis
```

---

## Quick Tips

- Use `.lean()` for read-only queries — significantly faster since it skips Mongoose document hydration.
- Always use `runValidators: true` on updates if you want schema validation enforced (Mongoose skips it by default on updates).
- Prefer `$set` over full replacement in updates to avoid unintentionally wiping fields.
- Use projections (`.select()`) to avoid pulling unnecessary large fields (e.g., don't fetch full documents just to check existence — use `.exists()`).
- Index fields you frequently filter, sort, or join (`populate`) on.
- Use `.explain()` in mongosh to debug slow queries and verify index usage.