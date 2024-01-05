```
// Deep Dive Create

// Insert Hobbies
db.hobbies.insert([
  { _id: "Sports", name: "sports" },
  { _id: "Yoga", name: "yoga" },
  { _id: "Cooking", name: "cooking" },
  { _id: "Running", name: "running" },
  { _id: "Hiking", name: "hiking" }
], { ordered: false }); // If there is an existing record, do not create again

// Write Concern

// Insert Person with Write Concern 0
db.persons.insertOne({ name: "cristy", age: 41 }, { writeConcern: { w: 0 } });

// Insert Data (Response) Without Waiting for Server (acknowledge: false)
db.persons.insertOne(
  { name: "cristy", age: 41 },
  { writeConcern: { w: 1, j: false, wtimeout: 200 } }
);
// Acknowledge True
// ...
```