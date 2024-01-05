
db.capped.sort({ $natural : -1}).find()


// backup if primary node is offline
client -> mongoserver -> primary node -> secondary node(2)

// sharding
client -> mongos(router) -> mongodServer(3)(Shard Key)

// connect to production cluster
mongo "url" --username max 

Useful Articles/ Docs:

Official Docs on Replica Sets: https://docs.mongodb.com/manual/replication/

Official Docs on Sharding: https://docs.mongodb.com/manual/sharding/