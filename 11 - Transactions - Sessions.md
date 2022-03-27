# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 11 - Transactions - Sessions

## Table of Contents
- [Transactions - Session](#transactions-session)


## Transactions - Session
> - `db.getMongo().startSession()` - start the session
> - `getDatabase("database_name").collectionName` - getting the session for the specified collection of database
> - `startTransaction()` start transaction
> - `commitTransaction()` complete transaction
> - `abortTransaction()` abort the session
```js
// Starting session
const session = db.getMongo().startSession()

// Assigning session for collection of the database
const dataC = session.getDatabase("sample_weatherdata").data

// Starting
session.startTransaction()

dataC.deleteOne({type:"FM-13"}) // operation on collection session. Original collection is not affected
session.commitTransaction() // completing the session. Original collection is affected
session.abortTransaction() // abort the session. Original collection is not affected
```
