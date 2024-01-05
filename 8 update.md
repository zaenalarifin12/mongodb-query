```

=== update ====

db.persons.udpateOne({_id: ObjectId("5b9f707ead7ae74ebe5bc6c7")}, {$set: {hobbies: [{title: "Sports", frequency: 5}, {title: "Cooking", frequency: 3}, {title: "Hiking", frequency: 1}]}})

db.users.updateMany({"hobbies.title": "Sports"}, {$set: {isSporty: true}})

db.users.updateOne({_id: ObjectId("5b9f707ead7ae74ebe5bc6c7")}, {$set: {age: 40, phone: 483924792}})

db.users.updateOne({name: "Manuel"}, {$inc: {age: 2}})

db.users.updateOne({name: "Manuel"}, {$inc: {age: -1}})

db.users.updateOne({name: "Manuel"}, {$inc: {age: 1}, $set: {isSporty: false}})

// update to min if current value higher
db.users.updateOne({name: "Chris"}, {$min: {age: 35}})

// update to max if current value smaller
db.users.updateOne({name: "Chris"}, {$max: {age: 35}})

// update to multiply
db.users.updateOne({name: "Chris"}, {$mul: {age: 35}})

db.users.updateMany({isSporty: true}, {$unset: {phone: ""}})

// remove field if = ""
db.users.updateMany({isSporty: true}, {$unset: {phone: ""}}) 

// update field
db.users.updateMany({}, {$rename: {age: "totalAge"}})

// update or create || default false
db.users.updateOne({name: "Maria"}, {$set: {age: 29, hobbies: [{title: "Good food", frequency: 3}], isSporty: true}}, {upsert: true})

// ==== ARRAY ====
// updating matching array element
------------------
db.users.find({hobbies: {$elemMatch: {title: "Sports", frequency: {$gte: 3}}}}).pretty()

db.users.updateMany({hobbies: {$elemMatch: {title: "Sports", frequency: {$gte: 3}}}}, {$set: {"hobbies.$.highFrequency": true}})

{
  "_id": ObjectId("5b9f652dc62d13aa2e81c29d"),
  "hobbies": [
    {
      "title": "Sports",
      "frequency": 3,
      "highFrequency": true
    },
    {
      "title": "Cooking",
      "frequency": 6
    }
  ],
  "name": "Max",
  "isSporty": true
}

----------------
// updating all array element
db.users.find({age: {$gt: 30}}).pretty()
db.users.find({totalAge: {$gt: 30}}).pretty()

db.users.updateMany({totalAge: {$gt: 30}}, {$inc: {"hobbies.$[].frequency": -1}})

// frequency decrement 1
{
  "_id": ObjectId("5b9f707ead7ae74ebe5bc6c7"),
  "name": "Chris",
  "hobbies": [
    {
      "title": "Sports",
      "frequency": 4,
      "highFrequency": true
    },
    {
      "title": "Cooking",
      "frequency": 2
    },
    {
      "title": "Hiking",
      "frequency": 0
    }
  ],
  "isSporty": true,
  "totalAge": 41.800000000000004
}
---------------------------------------
// finding and updating spesific field
$ = first matching element
$[] = all matching element

db.users.find({"hobbies.frequency": {$gt: 2}}).pretty()

db.users.updateMany({"hobbies.frequency": {$gt: 2}}, {$set: {"hobbies.$[el].goodFrequency": true}}, {arrayFilters: [{"el.frequency": {$gt: 2}}]})

{
  "_id": ObjectId("5b9f707ead7ae74ebe5bc6c7"),
  "name": "Chris",
  "hobbies": [
    {
      "title": "Sports",
      "frequency": 4,
      "highFrequency": true,
      "goodFrequency": true
    },
    {
      "title": "Cooking",
      "frequency": 2
    },
    {
      "title": "Hiking",
      "frequency": 0
    }
  ],
  "isSporty": true,
  "totalAge": 41.800000000000004
}
----------------
// finding and updating spesific field
// adding elements to array

db.users.updateOne({name: "Maria"}, {$push: {hobbies: {title: "Sports", frequency: 2}}})

db.users.updateOne({name: "Maria"}, {$push: {hobbies: {$each: [{title: "Good Wine", frequency: 1}, {title: "Hiking", frequency: 2}], $sort: {frequency: -1}}}})
----------------------

// remove element from array

// remove where title ....
db.users.updateOne({name: "Maria"}, {$pull: {hobbies: {title: "Hiking"}}})

// remove last element array
// 1 = last element
// -1 = first element

db.users.updateOne({name: "Maria"}, {$pop: {hobbies: 1}})
---------------------------

// $addToSet -> insert unique value in array
not insert again if you duplicate
db.users.updateOne({name: "maria"}, {$addToSet: {title: "hiking", frequency: 2}})

```