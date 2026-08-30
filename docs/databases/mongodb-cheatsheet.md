# MongoDB Cheatsheet

## Checking if MongoDB is Installed and Running

### 1. Check if MongoDB is Installed
```bash
# Check MongoDB version
mongod --version

# Check MongoDB client version
mongo --version

# For newer versions (MongoDB 6+), use:
mongosh --version
```

### 2. Check if MongoDB Service is Running

**On Windows:**
```bash
# Check if MongoDB service is running
sc query MongoDB

# Start MongoDB service
net start MongoDB

# Stop MongoDB service
net stop MongoDB

# Check MongoDB processes
tasklist | findstr mongod
```

**On Linux/macOS:**
```bash
# Check if MongoDB is running
sudo systemctl status mongod

# Start MongoDB
sudo systemctl start mongod

# Stop MongoDB
sudo systemctl stop mongod

# Check MongoDB processes
ps aux | grep mongod
```

### 3. Test MongoDB Connection
```bash
# Connect to MongoDB (older versions)
mongo

# Connect to MongoDB (newer versions 6+)
mongosh

# Connect to specific database
mongosh --host localhost --port 27017

# Connect with authentication
mongosh -u username -p password --authenticationDatabase admin
```

## MongoDB Cheatsheet

### Basic Connection Commands
```bash
# Connect to MongoDB
mongosh

# Connect to specific database
use database_name

# Show current database
db

# Show all databases
show dbs

# Show collections in current database
show collections
```

### Database Operations
```javascript
// Create/switch to database
use myDatabase

// Drop current database
db.dropDatabase()

// Get database stats
db.stats()
```

### Collection Operations
```javascript
// Create collection
db.createCollection("myCollection")

// Drop collection
db.myCollection.drop()

// Get collection stats
db.myCollection.stats()

// Count documents
db.myCollection.countDocuments()

// Get collection info
db.myCollection.getIndexes()
```

### CRUD Operations

#### Create (Insert)
```javascript
// Insert single document
db.users.insertOne({
  name: "John Doe",
  email: "john@example.com",
  age: 30
})

// Insert multiple documents
db.users.insertMany([
  { name: "Alice", email: "alice@example.com", age: 25 },
  { name: "Bob", email: "bob@example.com", age: 35 }
])

// Insert with custom _id
db.users.insertOne({
  _id: "user123",
  name: "Jane",
  email: "jane@example.com"
})
```

#### Read (Find)
```javascript
// Find all documents
db.users.find()

// Find with pretty formatting
db.users.find().pretty()

// Find one document
db.users.findOne()

// Find with filter
db.users.find({ age: 30 })

// Find with multiple conditions
db.users.find({ age: { $gte: 25 }, name: "John" })

// Find with projection (select specific fields)
db.users.find({}, { name: 1, email: 1, _id: 0 })

// Find with sorting
db.users.find().sort({ age: 1 })  // ascending
db.users.find().sort({ age: -1 }) // descending

// Find with limit
db.users.find().limit(5)

// Find with skip
db.users.find().skip(10).limit(5)

// Find with regex
db.users.find({ name: /^J/ })  // names starting with J
```

#### Update
```javascript
// Update single document
db.users.updateOne(
  { name: "John Doe" },
  { $set: { age: 31 } }
)

// Update multiple documents
db.users.updateMany(
  { age: { $lt: 30 } },
  { $set: { status: "young" } }
)

// Replace document
db.users.replaceOne(
  { name: "John Doe" },
  { name: "John Smith", email: "johnsmith@example.com", age: 31 }
)

// Upsert (insert if not exists)
db.users.updateOne(
  { email: "newuser@example.com" },
  { $set: { name: "New User", age: 25 } },
  { upsert: true }
)
```

#### Delete
```javascript
// Delete single document
db.users.deleteOne({ name: "John Doe" })

// Delete multiple documents
db.users.deleteMany({ age: { $lt: 18 } })

// Delete all documents in collection
db.users.deleteMany({})
```

### Query Operators

#### Comparison Operators
```javascript
// Equal
db.users.find({ age: 30 })

// Not equal
db.users.find({ age: { $ne: 30 } })

// Greater than
db.users.find({ age: { $gt: 25 } })

// Greater than or equal
db.users.find({ age: { $gte: 25 } })

// Less than
db.users.find({ age: { $lt: 50 } })

// Less than or equal
db.users.find({ age: { $lte: 50 } })

// In array
db.users.find({ age: { $in: [25, 30, 35] } })

// Not in array
db.users.find({ age: { $nin: [25, 30, 35] } })

// Exists
db.users.find({ email: { $exists: true } })

// Type check
db.users.find({ age: { $type: "number" } })
```

#### Logical Operators
```javascript
// AND
db.users.find({ age: { $gte: 25 }, name: "John" })

// OR
db.users.find({ $or: [{ age: { $lt: 25 } }, { age: { $gt: 50 } }] })

// NOT
db.users.find({ age: { $not: { $gt: 30 } } })

// NOR
db.users.find({ $nor: [{ age: { $lt: 25 } }, { name: "John" }] })
```

### Indexing
```javascript
// Create single field index
db.users.createIndex({ email: 1 })

// Create compound index
db.users.createIndex({ name: 1, age: -1 })

// Create text index
db.users.createIndex({ name: "text", email: "text" })

// List indexes
db.users.getIndexes()

// Drop index
db.users.dropIndex({ email: 1 })

// Drop all indexes except _id
db.users.dropIndexes()
```

### Aggregation Pipeline
```javascript
// Basic aggregation
db.users.aggregate([
  { $match: { age: { $gte: 25 } } },
  { $group: { _id: "$department", count: { $sum: 1 } } },
  { $sort: { count: -1 } }
])

// Group and calculate averages
db.users.aggregate([
  { $group: { 
    _id: "$department", 
    avgAge: { $avg: "$age" },
    count: { $sum: 1 }
  }}
])

// Lookup (join)
db.orders.aggregate([
  { $lookup: {
    from: "users",
    localField: "userId",
    foreignField: "_id",
    as: "user"
  }}
])
```

### Backup and Restore
```bash
# Backup database
mongodump --db myDatabase --out /backup/path

# Restore database
mongorestore --db myDatabase /backup/path/myDatabase

# Export collection to JSON
mongoexport --db myDatabase --collection users --out users.json

# Import from JSON
mongoimport --db myDatabase --collection users --file users.json
```

### User Management
```javascript
// Create user
db.createUser({
  user: "myuser",
  pwd: "mypassword",
  roles: ["readWrite"]
})

// List users
db.getUsers()

// Update user
db.updateUser("myuser", { roles: ["readWrite", "dbAdmin"] })

// Drop user
db.dropUser("myuser")
```

### Monitoring and Performance
```javascript
// Show current operations
db.currentOp()

// Show server status
db.serverStatus()

// Show database stats
db.stats()

// Explain query execution
db.users.find({ age: 30 }).explain()

// Profile queries
db.setProfilingLevel(2)  // profile all operations
db.setProfilingLevel(1, { slowms: 100 })  // profile slow operations
db.system.profile.find().limit(5).sort({ ts: -1 }).pretty()
```

### Common Troubleshooting Commands
```javascript
// Check connection
db.runCommand({ ping: 1 })

// Get server version
db.version()

// Check if database exists
db.adminCommand("listDatabases")

// Get collection size
db.users.dataSize()

// Get storage size
db.users.storageSize()

// Compact collection
db.users.compact()
```