```
======= DELETE OPERATION =============

db.users.deleteOne({name: "Chris"})

db.users.deleteMany({age: {$gt: 30}}, isSporty: true)


// value field is "null"
db.users.deleteMany({age: {$exist: false}}, isSporty: true)

db.users.drop()

db.dropDatabase()
```