# MongoDB Shell (mongosh) Template

This template outlines connection details and security best practices for `mongosh`.

## Security & Usage Guidelines

- **Authentication MUST use the environment variables specified below (e.g., `$DIP_MONGODB_USER`, `$DIP_MONGODB_PASS`).**
- Never hardcode or expose sensitive credentials like passwords or full connection strings directly in commands or scripts.
- Always rely on the defined environment variables for sensitive connection details.
- Be cautious and warn users before executing potentially destructive operations such as `db.dropDatabase()`, `collection.drop()`, `collection.remove({})`, `collection.deleteOne()`, `collection.deleteMany({})`, `collection.updateMany({}, { $set: ... })` with broad criteria, etc.
- When possible, after performing write operations (e.g., inserting, updating, or deleting data), consider a follow-up read operation (e.g., `find()`, `findOne()`) to verify the action's success and impact.

## Connection Information (using environment variables)

- **Username:** Use environment variable `DIP_MONGODB_USER`
- **Password:** Use environment variable `DIP_MONGODB_PASS`
- **Server Address:** Use environment variable `DIP_MONGODB_SERVER` (e.g., `mongodb://localhost:27017` or `mongodb+srv://cluster.mongodb.net`)
- **Database Name:** Use environment variable `DIP_MONGODB_DBNAME`

## Example Connection Command

Connect using a command similar to this, substituting environment variables:

```bash
mongosh "$DIP_MONGODB_SERVER/$DIP_MONGODB_DBNAME" --username "$DIP_MONGODB_USER" --password "$DIP_MONGODB_PASS" --authenticationDatabase <auth_db>
```

*Note: Replace `<auth_db>` with the appropriate authentication database (often `admin` or the user's specific database, as indicated in `custom/mongodb.mongosh.md` if available).* 

## Common CRUD Operations Examples

These examples assume you are connected to the database using `mongosh`. Replace `myCollection` with your actual collection name.

### Insert Documents

Insert a single document:
```javascript
db.myCollection.insertOne(
  {
    field1: "value1",
    field2: 123,
    tags: ["tagA", "tagB"]
  }
)
```

Insert multiple documents:
```javascript
db.myCollection.insertMany([
   { fieldA: "valueA", count: 5 },
   { fieldA: "valueB", count: 10 }
])
```
[More Insert Examples...](https://www.mongodb.com/docs/mongodb-shell/crud/insert/)

### Read (Query) Documents

Find all documents in a collection:
```javascript
db.myCollection.find()
```

Find documents matching a specific condition:
```javascript
db.myCollection.find( { field1: "value1" } )
```

Find documents using query operators (e.g., find documents where `count` is greater than 5):
```javascript
db.myCollection.find( { count: { $gt: 5 } } )
```
[More Query Examples...](https://www.mongodb.com/docs/mongodb-shell/crud/read/)

### Update Documents

Update the first document matching a filter:
```javascript
db.myCollection.updateOne(
   { field1: "value1" }, // Filter
   { $set: { field2: 456, status: "updated" } } // Update operations
)
```

Update all documents matching a filter:
```javascript
db.myCollection.updateMany(
   { status: "initial" }, // Filter
   { $set: { status: "processed" }, $currentDate: { lastModified: true } } // Update operations
)
```
[More Update Examples...](https://www.mongodb.com/docs/mongodb-shell/crud/update/)

### Delete Documents

Delete the first document matching a filter:
```javascript
db.myCollection.deleteOne( { status: "obsolete" } )
```

Delete all documents matching a filter:
```javascript
db.myCollection.deleteMany( { count: { $lt: 10 } } )
```
[More Delete Examples...](https://www.mongodb.com/docs/mongodb-shell/crud/delete/)

---

*For more comprehensive details and advanced operations, refer to the official [MongoDB Shell CRUD Documentation](https://www.mongodb.com/docs/mongodb-shell/crud/).* 