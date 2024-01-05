```
// Introduction

// Basic CRUD Operations
mongosh
use shop

// Insert Documents
db.products.insertOne({ name: "a book", price: 1200 })
db.products.insertOne({ name: "a book", price: 1200, _id: "ABK" })

// Query Documents
db.products.find()
db.products.find().pretty()

// More Inserts
db.products.insertOne({ name: "A T-Shirt", price: 12.99, description: "Pretty T-Shirt" })
db.products.insertOne({ name: "A Computer", price: 1299.99, description: "A Best Computer", details: { cpu: "Intel i9", memory: 32 } })

// CRUD Operations

// Update Documents
db.flightData.updateOne({ "distance": 1200 }, { $set: { "marker": "delete" } })
db.flightData.updateOne({ "_id": "xxxxxxxxxxxxxxxxx" }, { $set: { "marker": "delete" } })

// Query Documents
db.flightData.find({ distance: 10000 })
db.flightData.find({ distance: { $gt: 1000 } }) // >

// Displaying Results
db.flightData.find().toArray() // in shell more than 20 rows
db.flightData.find().forEach((passengerData) => { printjson(passengerData) })
db.passengers.find({}, { name: 1, _id: 0 }).pretty()

// More Queries
db.passengers.updateOne({ name: "aba" }, { $set: { hobbies: ["sport", "cooking"] } })
db.passengers.find({ name: "aba" }).hobbies
db.passengers.find({ hobbies: "sport" })

// Nested Query
db.flightData.find({ "status.description": "on-time" })

// Task Assignment

// Insert Documents
db.patients.insertMany([
  { firstName: "Max", lastName: "Schwarzmueller", age: 29, history: [{ disease: "cold", treatment: 1 }] },
  { firstName: "Manu", lastName: "Lorenz", age: 30, history: [{ disease: "hungry", treatment: 95 }] },
  { firstName: "Maria", lastName: "Blake", age: 26, history: [{ disease: "sad", treatment: 55 }] }
])

// Query Documents
db.patients.find()

// Update Document
db.patients.updateOne(
  { firstName: "Maria" },
  { $set: { lastName: "Blakes", age: 27, history: [{ disease: "very sad", treatment: 61 }] } }
)

// Reset Database
db.dropDatabase()

// Additional Topics
// - Data & Schema
// - Working in the Shell
// - Using Compass
// - CRUD Deep Dive
// - Using Indexes
// - Working with Geospatial Data
// - The Aggregation Network
// - Working with Numeric Data
// - Security & Authentication
// - Performance, Fault Tolerance, & Deployment
// - Transaction
// - From Shell to Driver
// - MongoDB Stitch
```