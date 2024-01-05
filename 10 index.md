```
==== MONGODB ====

if no index, search will scan all collection and then filter

index = jump

db.persons.explain().find({"dob.age": {$gt : 60} })

db.persons.explain("executionStats).find({"dob.age": {$gt : 60} })
---->>> exectionTimeMillis == 
---->>> inputStage -> stage -> IXSCAN
totalDocsExamined

// index ascending | descending
db.persons.createIndex({"dob.age": 1})

======================================================
                INDEX RESTRICTIONS

db.contacts.dropIndex({"dob.age" : 1})

======================================================
                COMPOUND INDEX
DB.contacts.createIndex({"dob.age" : 1, gender : 1 })

DB.contacts.explain().find({"dob.age" : 35, gender : "male" })

======================================================
                INDEX FOR SORTING
DB.contacts.explain("").find({"dob.age" : 35}).sort({gender: 1})
32 mb memory

======================================================
                DEFAULT INDEX
db.contacts.getIndexes()

======================================================
                CONFIGURING INDEX
db.contacts.createIndex({email: 1}, {unique: true})

======================================================
                PARTIAL INDEX
// index where clause
db.contacts.createIndex({"dob.age" : 1}, { partialFilterExpression: {"dob.age" : {$gt : 60}} })

db.contacts.find({"dob.age" : {$gt : 60}}) // connect with compound-index

// create index for email exist
db.users.createIndex({email: 1}, {unique: true, partialFilterExpression: {email: {$exists: true}}})

======================================================
                TIME TO LIVE INDEX

// data will be remove after seconds.. 
db.session.insertOne({data: "ABCDE", createdAt: new Date()})
db.session.find()
db.session.createIndex({createdAt: 1}, {expireAfterSeconds: 10})

=====================================================
                QUERY DIAGNOSIS & QUERY PLANNING

=====================================================
                REJECS A PLAN
![](1.png)
![](2.png)
db.customers.explain("allPlansExecution").find({name: "baba", age: 30})

=======================================================
                MULTI KEY INDEXES

db.customer.find({hobbies: "running"})
db.explain("executionStats").customer.find({hobbies: "running"})
// check -> isMultiKey: true

// index just 
db.customer.createIndex({"addressed.street": 1})

// rule multi key index [single, array]
if [ array , array] it will be error
db.contact.createIndex({name: 1, hobbies: 1})

=======================================================
                TEXT INDEXES
// maximum 1 collection 1 text index because expensive (long text)
db.books.createIndex({description: "text"})
db.books.find({$text: {$search: "awesome"}})
db.books.find({$text: {$search: "\"red book\""}}) // one sentence

// sorting and score
db.books.find({$text: {$search: "red book"}}, score: {$meta: "textScore"}).sort({score: {$meta: "textScore"}}).pretty() // one sentence

// combining text indexes
// this index will search title & description
db.books.createIndex({title: "text", description: "text"})

// exclude work
// not contain using -
db.books.find({$text: {$search: "awesome -T-shirt"}})

// setting language and using weight
db.books.createIndex({title: "text", description: "text"}, {default_language: "english", weights: {title: 1, description: 10}})
db.books.find({$text: {$search: "red book"}}, score: {$meta: "textScore"}).sort({score: {$meta: "textScore"}}).pretty() // one sentence
























```