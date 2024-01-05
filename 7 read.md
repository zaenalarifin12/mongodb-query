```
// Basic find query
db.myCollection.find({ age: 32 })

// Find documents where age is greater than 30
db.myCollection.find({ age: { $gt: 30 } })

// Query Operators Table
// | Type               | Purpose                    | Example  |
// |--------------------|----------------------------|----------|
// | Query Operator     | Locate data                | $eq      |
// | Projection Operator| Modify presentation        | $        |
// | Update Operator     | Modify & add additional    | $inc     |

// Query Selector Types
// Comparison | Evaluation
// Logical     | Array
// Element     | Comments
// Geospatial

// Projection Operators
// $, $elemMatch, $meta, $slice

// Find one document
db.myCollection.findOne({})

// Find documents based on various conditions
db.movies.find({ "rating.average": { $gt: 1.1 } }) // Nested object
db.movies.find({ genre: "Drama" }) // Array
db.movies.find({ runtime: { $in: [20, 40] } }) // IN
db.movies.find({ runtime: { $nin: [20, 40] } }) // NOT IN

// Logical operators
db.movies.find({
  $or: [
    { "rating.average": { $lt: 5 } },
    { "rating.average": { $gt: 9.3 } }
  ]
})

db.movies.find({
  $nor: [
    { "rating.average": { $lt: 5 } },
    { "rating.average": { $gt: 9.3 } }
  ]
}).count() // Neither of the two conditions

// Combining conditions with $and
db.movies.find({
  $and: [
    { genre: "Action" },
    { release_year: { $gt: 2010 } }
  ]
})

// Combining conditions with $not
db.movies.find({
  $and: [
    { genre: { $not: { $eq: "Action" } } },
    { release_year: { $not: { $gt: 2010 } } }
  ]
})

// Simplified version using $ne
db.movies.find({
  $and: [
    { genre: { $ne: "Action" } },
    { release_year: { $not: { $gt: 2010 } } }
  ]
})

db.users.find({
  "age": { $exists: true }
})

// BSON Types
// Reference: https://www.mongodb.com/docs/manual/reference/bson-types/
db.users.find({
  "age": { $type: <BSONTypeCode> }
})
db.users.find({
  "age": { $type: 16 }
})
db.collection.find({
  "your_field": { $in: [2, 4] }
})

db.collection.find({
  "email": { $regex: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/ }
})

db.sales.find({
  $expr: {
    $gt: [
      {
        $cond: {
          if: { $gte: ["$volume", 190] },
          then: { $subtract: ["$volume", 30] },
          else: "$volume"
        }
      },
      "$target"
    ]
  }
}).pretty()

// Deep dive into arrays
// Select from value array [{title : ....}]
db.users.find({ "hobbies.title": "Sports" }).pretty()

db.users.find({
  "interests": { $size: 3 }
})
db.users.find({
  "interests": { $all: ["reading", "traveling"] }
})
db.users.find({
  "interests": {
    $elemMatch: {
      $in: ["reading", "traveling"]
    }
  }
})
db.users.find({ hobbies: { $elemMatch: { title: "Sports", frequency: { $gte: 3 } } } }).pretty()

// Cursor operations
// Reference: https://www.mongodb.com/docs/manual/tutorial/iterate-a-cursor/
const cursor = db.movies.find()
cursor.hasNext()
cursor.next()
cursor.sort({ "rating.average": 1, runtime: -1 }).pretty() // Desc if 1, Asc if -1
db.movies.find().sort({ "rating.average": 1, runtime: -1 }).skip(100).limit(10).count()
db.movies.find({}, { name: 1, genres: 1, runtime: 1, "rating.average": 1, _id: 0 }).pretty() // Select only specified fields
db.movies.find({ genres: { $all: ["Drama", "Horror"] } }, { "genres.$": 1 }).pretty() // Select in array
db.movies.find({ genres: "Drama" }, { genres: { $elemMatch: { $eq: "Horror" } } }).pretty()
db.movies.find({ "rating.average": { $gt: 9 } }, { genres: { $slice: [1, 2] }, name: 1 }).pretty() // Choose array elements 1 - 2
```