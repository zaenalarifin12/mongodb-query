const session = db.getMongo().startSession()
session.startTransaction()
const usersCol = session.getDatabase("blog").users
const postsCol = session.getDatabase("blog").posts
// query delete user
// query delete post
session.commitTransaction()
session.abortTransaction()

Official Docs on Transactions: https://docs.mongodb.com/manual/core/transactions/