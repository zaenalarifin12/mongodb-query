// ======= AGREGATION =============

// https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/

==============================================
db.persons
  .aggregate([
    { $match: { gender: "female" } },
    {
      $group: { _id: { state: "$location.state" }, totalPersons: { $sum: 1 } },
    },
    { $sort: { totalPersons: -1 } },
  ])
  .pretty();

db.person.find({ "location.state": "sinop" });

// RESULT 

{
  "_id" : { "state" : "California" },
  "totalPersons" : 120
}
{
  "_id" : { "state" : "New York" },
  "totalPersons" : 90
}

{
  "_id" : ObjectId("some_object_id"),
  "name" : "Jane Doe",
  "age" : 28,
  "gender" : "female",
  "location" : {
    "state" : "Sinop",
    "city" : "Some City"
  }
}

======================================
// --- $PROJECT -----

db.persons
  .aggregate([
    {
      $project: {
        _id: 0,
        gender: 1,
        fullName: {
          $concat: [
            {
              $toUpper: { $substrCP: ["$name.first", 0, 1] },
            },
            {
              $substrCP: [
                "$name.first",
                1,
                { $subtract: [{ $strLenCP: "$name.first" }, 1] },
              ],
            },
            " ",
            {
              $toUpper: { $substrCP: ["$name.last", 0, 1] },
            },
            {
              $substrCP: [
                "$name.last",
                1,
                { $subtract: [{ $strLenCP: "$name.last" }, 1] },
              ],
            },
          ],
        },
      },
    },
  ])
  .pretty();

// RESULT 
{ "gender" : "male", "fullName" : "John Doe" }
{ "gender" : "female", "fullName" : "Jane Smith" }


=======================================
// turning the location into a geoJSON Object
db.persons
  .aggregate([
    {
      $project: {
        _id: 0,
        name: 1,
        email: 1,
        birthdate: { $toDate: "$dob.date" },
        age: "$dob.age",
        location: {
          type: "Point",
          coordinates: [
            {
              $convert: {
                input: "$location.coordinates.longitude",
                to: "double",
                onError: 0.0,
                onNull: 0.0,
              },
            },
            {
              $convert: {
                input: "$location.coordinates.latitude",
                to: "double",
                onError: 0.0,
                onNull: 0.0,
              },
            },
          ],
        },
      },
    },
    {
      $project: {
        _id: 0,
        gender: 1,
        email: 1,
        birthdate: 1,
        location: 1,
        fullName: {
          $concat: [
            {
              $toUpper: { $substrCP: ["$name.first", 0, 1] },
            },
            {
              $substrCP: [
                "$name.first",
                1,
                { $subtract: [{ $strLenCP: "$name.first" }, 1] },
              ],
            },
            " ",
            {
              $toUpper: { $substrCP: ["$name.last", 0, 1] },
            },
            {
              $substrCP: [
                "$name.last",
                1,
                { $subtract: [{ $strLenCP: "$name.last" }, 1] },
              ],
            },
          ],
        },
      },
    },
    {
      $group: {
        _id: { birthYear: { $isoWeekYear: "$birthdate" } },
        numPersons: { $sum: 1 },
      },
    },
    { $sort: { numPersons: -1 } },
  ])
  .pretty();

// RESULT 
{
  "_id" : { "birthYear" : 1990 },
  "numPersons" : 120
}
{
  "_id" : { "birthYear" : 1985 },
  "numPersons" : 90
}

================================================
/**
 * $group								$project
 * n:1									1:1
 *||| -> | 								| -> |
 * sum, count, avgm build array			include/exclude field, transform fields (within a single document)
 *
 *
 */

===============================================
//  Pushing Elements Into Newly Created Array
db.friends
  .aggregate([
	{ $unwind: "$hobbies"}, // the documents are deconstructed, and each document represents a single hobby
    { $group: { _id: { age: "$age" }, allHobbies: { $addToSet: "$hobbies" } } },  // operator is used to add unique values to the array.
  ])
  .pretty();

// RESULT
{
  "_id" : { "age" : 25 },
  "allHobbies" : [ "Reading", "Traveling", "Photography" ]
}
{
  "_id" : { "age" : 30 },
  "allHobbies" : [ "Cooking", "Hiking", "Gardening" ]
}


// 
db.friends
  .aggregate([
	{ $project : { _id: 0, examScore : {$slice: ["$examScores", 2, 1]}} }
]).pretty()

==================================================
// getting length of an array
db.friends
  .aggregate([
	{ $project : { _id: 0, numScores : {$size: ["$examScores"]}} }
]).pretty()

{ numScores: 3 }
{ numScores: 3 }
{ numScores: 3 }
=================================================

// filter
db.friends
  .aggregate([
    {
      $project: {
        _id: 0,
        scores : { $filter: {input: "$examScores", as : "sc" , cond: { $gt : ["$$sc.score", 60]}}},
      },
    },
  ])
  .pretty();

{
    "scores": [
      {
        "difficulty": 6,
        "score": 62.1
      },
      {
        "difficulty": 3,
        "score": 88.5
      }
    ]
  },
  {
    "scores": [
      {
        "difficulty": 2,
        "score": 74.3
      }
    ]
  },
  {
    "scores": [
      {
        "difficulty": 3,
        "score": 75.1
      },
      {
        "difficulty": 6,
        "score": 61.5
      }
    ]
  }

=============================

db.friends.aggregate([
  { $unwind: "$examScores" },
  { $project: { _id: 1, name: 1, age: 1, score: "$examScores.score" } },
  { $sort: { score: -1 } },
  {
    $group: {
      _id: "$_id",
      name: { $first: "$name" },
      maxScore: { $max: "$score" },
    },
  },
  { $sort: { maxScore: -1 } },
]).pretty();

[
  {
    "_id": {"$oid": "658ba2d4e72591182b3b5fe1"},
    "maxScore": 88.5,
    "name": "Max"
  },
  {
    "_id": {"$oid": "658ba2d4e72591182b3b5fe3"},
    "maxScore": 75.1,
    "name": "Maria"
  },
  {
    "_id": {"$oid": "658ba2d4e72591182b3b5fe2"},
    "maxScore": 74.3,
    "name": "Manu"
  }
]

===========================================
db.persons.aggregate([
  {
    $bucket: {
      groupBy: "$dob.age",
      boundaries: [0, 18, 30, 50, 80, 120],
      output: {
        numPersons : { $sum: 1},
        averageAge : { $avg: "$dob.age"},
        names : { $push: "$name.first"},
         
      }
    }
  }
]).pretty()

[
  {
    "_id": 18,
    "averageAge": 25.101382488479263,
    "numPersons": 868
  },
  {
    "_id": 30,
    "averageAge": 39.4917943107221,
    "numPersons": 1828
  },
  {
    "_id": 50,
    "averageAge": 61.46440972222222,
    "numPersons": 2304
  }
]

==========================================
db.persons.aggregate([
  {
    $bucketAuto: {
      groupBy: "$dob.age",
      bucket: 5,
      output: {
        numPersons : { $sum: 1},
        averageAge : { $avg: "$dob.age"}, 
      }
    }
  }
]).pretty()

[
  {
    "_id": {
      "min": 21,
      "max": 32
    },
    "averageAge": 25.99616122840691,
    "numPersons": 1042
  },
  {
    "_id": {
      "min": 32,
      "max": 43
    },
    "averageAge": 36.97722772277228,
    "numPersons": 1010
  },
  {
    "_id": {
      "min": 43,
      "max": 54
    },
    "averageAge": 47.98838334946757,
    "numPersons": 1033
  },
  {
    "_id": {
      "min": 54,
      "max": 65
    },
    "averageAge": 58.99342105263158,
    "numPersons": 1064
  },
  {
    "_id": {
      "min": 65,
      "max": 74
    },
    "averageAge": 69.11515863689776,
    "numPersons": 851
  }
]

==================================================
db.persons.aggregate([
  { $match: { gender: "male" } },
  {
    $project: {
      _id: 0,
      gender: 1,
      name: { $concat: ["$name.first", " ", "$name.last"] },
    birthDate: { $toDate: "$dob.date"}
    },
  },
  { $sort: { birthDate: 1 } },
  { $skip: 10 },
  { $limit: 10 },
]);

[
  {
    "birthDate": {"$date": "1944-09-12T07:49:20.000Z"},
    "gender": "male",
    "name": "عباس یاسمی"
  },
  {
    "birthDate": {"$date": "1944-09-16T16:03:28.000Z"},
    "gender": "male",
    "name": "پرهام جعفری"
  },
  {
    "birthDate": {"$date": "1944-09-17T15:04:13.000Z"},
    "gender": "male",
    "name": "eli henry"
  },
  {
    "birthDate": {"$date": "1944-09-18T11:03:05.000Z"},
    "gender": "male",
    "name": "kirk brown"
  },
  {
    "birthDate": {"$date": "1944-10-13T15:29:05.000Z"},
    "gender": "male",
    "name": "sebastian olsen"
  }
]

https://docs.mongodb.com/manual/core/aggregation-pipeline-optimization/

==================================================

select and the resutl create collection

db.persons
  .aggregate([
    {
      $project: {
        _id: 0,
        gender: 1,
        fullName: {
          $concat: [
            {
              $toUpper: { $substrCP: ["$name.first", 0, 1] },
            },
            {
              $substrCP: [
                "$name.first",
                1,
                { $subtract: [{ $strLenCP: "$name.first" }, 1] },
              ],
            },
            " ",
            {
              $toUpper: { $substrCP: ["$name.last", 0, 1] },
            },
            {
              $substrCP: [
                "$name.last",
                1,
                { $subtract: [{ $strLenCP: "$name.last" }, 1] },
              ],
            },
          ],
        },
      },
    },
    {
      $out: "transformedPersons"
    }
  ])
  .pretty();

==========================================

db.transformedPersons.aggregate([
  {
    $geoNear: {
      near: {
        type: "Point",
        coordinates: [-18.4, -42.8]
      },
      maxDistance: 10000,
      num: 10,
      query: {
        gender: { "male" },
        distanceField: "distance"

      }
    }
  }
])

https://docs.mongodb.com/manual/core/aggregation-pipeline/
$cond: https://docs.mongodb.com/manual/reference/operator/aggregation/cond/