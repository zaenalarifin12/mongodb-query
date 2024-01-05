```
// ========== Data Types ==========

// Text
let text = "Max";

// Boolean
let bool = true;

// Number
let numberInt32 = 55;

// NumberLong (int64)
let numberLong = NumberLong(10000000000);

// NumberDecimal
let numberDecimal = 12.99;

// ObjectId
let objectId = ObjectId("sfasd");

// ISODate
let isoDate = ISODate("2018-09-09");

// Embedded Document
let embeddedDocument = { "a": { /* ... */ } };

// Array
let array = { "b": [ /* ... */ ] };

// Timestamp
let timestamp = Timestamp(11421532);

// Insert Document
db.companies.insertOne({
  name: "Fresh Apples Inc",
  isStartup: true,
  employees: 33,
  funding: 12345678901234567890,
  details: { ceo: "Mark Super" },
  tags: [{ title: "super" }, { title: "perfect" }],
  foundingDate: new Date(),
  insertedAt: new Timestamp()
});

// One to Many
use cityData;
db.cities.insertOne({ name: "New York", coordinates: { lat: 22, lng: 56 } });
db.cities.findOne();
db.citizens.insertMany([
  { name: "Baba", cityId: ObjectId("658366dfa049f5be0233bcf2") },
  { name: "Haha", cityId: ObjectId("658366dfa049f5be0233bcf2") }
]);

// Aggregate
db.books.aggregate([
  {
    $lookup: {
      from: "authors",
      localField: "authors",
      foreignField: "_id",
      as: "creators"
    }
  }
]);

// Exercise
use blog;
db.users.insertMany([
  { name: "Baba", age: 29, email: "baba@gmail.com" },
  { name: "Haha", age: 29, email: "haha@gmail.com" }
]);

db.posts.insertOne({
  title: "my-first-blog",
  text: "this is text blog",
  tag: ["new", "tech"],
  creator: ObjectId("65838a04d6b73e3e897f9b23"),
  comments: [
    {
      text: "I like this post",
      author: ObjectId("65838a04d6b73e3e897f9b24")
    }
  ]
});

// Create Collection with Validator
db.createCollection("posts", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "text", "creator", "comments"],
      properties: {
        title: { bsonType: "string", description: "must be a string and is required" },
        text: { bsonType: "string", description: "must be a string and is required" },
        creator: { bsonType: "objectId", description: "must be an ObjectId and is required" },
        comments: {
          bsonType: "array",
          description: "must be an array and is required",
          items: {
            bsonType: "object",
            properties: {
              text: { bsonType: "string", description: "must be a string and is required" },
              author: { bsonType: "objectId", description: "must be an ObjectId and is required" }
            }
          }
        }
      }
    }
  }
});

// Update Validator
db.runCommand({
  collMod: 'posts',
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: { bsonType: 'string', description: 'must be a string and is required' },
        text: { bsonType: 'string', description: 'must be a string and is required' },
        creator: { bsonType: 'objectId', description: 'must be an ObjectId and is required' },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: { bsonType: 'string', description: 'must be a string and is required' },
              author: { bsonType: 'objectId', description: 'must be an ObjectId and is required' }
            }
          }
        }
      }
    }
  },
  validationAction: 'warn' // error , none
});
```