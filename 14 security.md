
use admin
// assign role
db.createUser({ user: "max", pwd: "max", roles: ["userAdminAnyDatabase"]}) 
// make credential
db.auth('max', "max")

// login user
mongo -u max -p max --authenticationDatabase admin

db.logout()

db.updateUser("append", {pwd: "aa", roles : [{"readWrite", db: "blog"}, {"readWrite", db: "blog"}]})

db.getUser("max")

// SSL
sudo mongod --sslMode requireSSL --sslPEMKeyFile mongodb.pem

mongo --ssl --sslCAFile mongodb.pem
===============
Helpful Articles/ Docs:

Official "Encryption at Rest" Docs: https://docs.mongodb.com/manual/core/security-encryption-at-rest/

Official Security Checklist: https://docs.mongodb.com/manual/administration/security-checklist/

What is SSL/ TLS? => https://www.acunetix.com/blog/articles/tls-security-what-is-tls-ssl-part-1/

Official MongoDB SSL Setup Docs: https://docs.mongodb.com/manual/tutorial/configure-ssl/

Official MongoDB Users & Auth Docs: https://docs.mongodb.com/manual/core/authentication/

Official Built-in Roles Docs: https://docs.mongodb.com/manual/core/security-built-in-roles/

Official Custom Roles Docs: https://docs.mongodb.com/manual/core/security-user-defined-roles/