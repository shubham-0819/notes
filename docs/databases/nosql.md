# NoSQL Databases

> **TODO:** This document is a stub. Expand with MongoDB queries, Redis patterns, Cassandra data modeling, and comparison of use cases.

## What is NoSQL?

NoSQL databases store and retrieve data in formats other than relational tables. They are designed for scalability, flexibility, and high performance with large volumes of unstructured or semi-structured data.

## Types of NoSQL Databases

| Type | Examples | Use Case |
|------|----------|----------|
| **Document** | MongoDB, CouchDB | JSON-like documents, flexible schema |
| **Key-Value** | Redis, DynamoDB | Caching, sessions, simple lookups |
| **Column-Family** | Cassandra, HBase | Time-series, write-heavy workloads |
| **Graph** | Neo4j, Amazon Neptune | Relationships, social networks |

## SQL vs NoSQL

| | SQL | NoSQL |
|-|-----|-------|
| Schema | Fixed schema | Flexible / schema-less |
| Scaling | Vertical | Horizontal |
| Transactions | Full ACID | Eventual consistency (usually) |
| Query Language | SQL | Database-specific API |
| Best For | Structured data, relationships | Scale, unstructured data |

## MongoDB Basics

```js
// Insert
db.users.insertOne({ name: "Alice", age: 30 });

// Find
db.users.find({ age: { $gt: 25 } });

// Update
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } });

// Delete
db.users.deleteOne({ name: "Alice" });

// Aggregation
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$userId", total: { $sum: "$amount" } } }
]);
```

## Redis Basics

```bash
# String
SET user:1:name "Alice"
GET user:1:name

# TTL (expiry)
SET session:abc "token" EX 3600

# Hash
HSET user:1 name Alice age 30
HGETALL user:1

# List
LPUSH queue task1
RPOP queue

# Set
SADD tags:post:1 "redis" "database"
SMEMBERS tags:post:1
```

## CAP Theorem in NoSQL

Most NoSQL databases favor **Availability + Partition Tolerance (AP)** over strict consistency:

- **MongoDB** — CP (configurable)
- **Cassandra** — AP (tunable consistency)
- **Redis** — CP (single node is consistent)

## References

- [MongoDB Docs](https://www.mongodb.com/docs/)
- [Redis Docs](https://redis.io/docs/)
