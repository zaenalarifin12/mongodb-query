========== GEOSPATIAL ================

db.places.insertOne({ name: "California academy", location : {
    type: "Point",
    coordinates : [
        -115.9228768,
        40.8469559
    ]
} })

db.places.createIndex({location: "2dsphere"})

db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: [-115.9228768,40.8469559]}, $maxDistance: 30, $minDistance: 10}}})

// finding places inside certain area
const p1 = [-115.9228768,40.8469559]
const p2 = [-115.9228768,40.8469559]
const p3 = [-115.9228768,40.8469559]
const p4 = [-115.9228768,40.8469559]
db.places.find({location: {$geoWithin: {$geometry: {type: "Polygon", coordinates: [[p1, p2, p3, p4]]}}}})


// finding out if a user is inside a spesific area
db.areas.insertOne({name : "Golden Gate Park", area: {type: "Polygon", coordinates: [[p1, p2, p3, p4]]}})
db.areas.createIndex({area : "2dsphere"})
db.areas.find({area : {$geoIntersects : {$geometry : {type: "Point", coordinates: [-6.878823533263115, 110.78946872262719]} } } })

// finding places within a certain radius
mencari tempat dalam radius tertentu
1 = 1 kilo meter
db.places.find({location: {$geowithin: {$centerSphere: [[-6.878823533263115, 110.78946872262719], 1 / 6371]}}})

// query sort
db.places.find(
  {
    location: {
      $nearSphere: {
        $geometry: {
          type: "Point",
          coordinates: [-6.878823533263115, 110.78946872262719]
        },
        $maxDistance: 1000 / 6371 // 1 kilometer in radians (you can adjust this value)
      }
    }
  }
).sort({ distance: 1 });
