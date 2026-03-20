# SQL Notes

> **TODO:** This document is a stub. Expand with advanced queries, window functions, indexes, query optimization, and database-specific notes.

## What is SQL?

SQL (Structured Query Language) is the standard language for interacting with relational databases.

## Core SQL Commands

### DDL — Data Definition Language

```sql
-- Create a table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Modify a table
ALTER TABLE users ADD COLUMN age INT;

-- Drop a table
DROP TABLE users;
```

### DML — Data Manipulation Language

```sql
-- Insert
INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');

-- Select
SELECT * FROM users;
SELECT name, email FROM users WHERE age > 25 ORDER BY name ASC;

-- Update
UPDATE users SET age = 30 WHERE name = 'Alice';

-- Delete
DELETE FROM users WHERE id = 1;
```

### Joins

```sql
-- INNER JOIN — rows that match in both tables
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN — all rows from left, matching from right
SELECT u.name, o.total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

### Aggregations

```sql
SELECT department, COUNT(*) AS headcount, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING COUNT(*) > 5
ORDER BY avg_salary DESC;
```

## ACID Properties

| Property | Description |
|----------|-------------|
| **Atomicity** | All operations in a transaction succeed or all fail |
| **Consistency** | DB moves from one valid state to another |
| **Isolation** | Concurrent transactions don't interfere |
| **Durability** | Committed data persists even after crash |

## Indexes

```sql
-- Create an index to speed up queries on email
CREATE INDEX idx_users_email ON users(email);

-- Composite index
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
```

- Indexes speed up reads but slow down writes
- Use on columns frequently used in `WHERE`, `JOIN`, or `ORDER BY`

## References

- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [SQLZoo](https://sqlzoo.net/)
