# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## Table of Contents
- [Check `mongosh` Version](#check-monosh-version)
- [Start the Mongo Shell](#start-the-mongo-shell)
- [Show Current Database](#show-current-database)
- [Show All Databases](#show-all-databases)
- [Create Or Switch Database](#create-or-switch-database)
- [Import data to Database](#import-data-to-database)
- [Drop Database](#drop-database)
- [Create Collection](#create-collection)
- [Show Collections](#show-collections)

## Check `mongosh` Version
```js
mongosh --version
```

## Start the Mongo Shell
```js
mongosh "YOUR_CONNECTION_STRING" --username YOUR_USER_NAME
```

## Show Current Database
```js
db
```

## Show All Databases
```js
show dbs
```

## Create Or Switch Database
#### Creates new database and starts using it if does not exist, or uses already existing database
```js
use blog
```

## Import data to Database
```js
mongoimport datafile.json -d databasename -c collectionname --jsonArray --drop
// --drop drop if exist, --jsonArray array, collectioname desired table name
```

## Drop Database
#### Delete database
```js
db.dropDatabase()
```

## Create Collection
```js
db.createCollection('posts')
```

## Show Collections
```js
show collections
```
