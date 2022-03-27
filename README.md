# MongoDB Cheat Sheet

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
- [Insert Document](#insert-document)
- [Insert Document with additional parameters](#insert-document-with-additional-parameters)
- [Insert Multiple Documents](#insert-multiple-documents)
- [Find All Documents](#find-all-documents)
- [Find One Document](#find-one-document)
- [Find Documents with Query](#find-documents-with-query)
- [Sort Documents](#sort-documents)
- [Count Documents](#count-documents)
- [Limit Documents](#limit-documents)
- [Chaining](#chaining)
- [Projection](#projection)
- [Comparison](#comparison)
- [Logical Operators](#logical-operators)
- [Evaluation Query (Regex, Expression)](#evaluation-query)
- [Element Query](#element-query)
- [Skip and Limit](#skip-and-limit)
- [Array](#array)
- [Cursor](#cursor)
- [Skip and Limit](#skip-and-limit)
- [Update Document](#update-document)
- [Update Document or Insert if not Found](#update-document-or-insert-if-not-found)
- [Increment Field (`$inc`)](#increment-field-inc)
- [Min - Max](#min-max)
- [Getting rid of field](#getting-rid-of-field)
- [Update Multiple Documents](#update-multiple-documents)
- [Rename Field](#rename-field)
- [Updating Arrays](#updating-arrays)
- [Delete a Document](#delete-a-document)
- [Delete Multiple Documents](#delete-multiple-documents)
- [Relations](#relations)
- [Query Diagnosis and Query Planning](#uery-diagnosis-and-query-planning)
- [Indexes](#indexes)
- [Create Index](#create-index)
- [Partial Index](#partial-index)
- [Text Index](#text-index)
- [Get Indexes](#get-index)
- [Delete Index](#delete-index)
- [Forefround Index vs Background Index](#forefround-index-vs-background-index)
- [Geolocation index](#geolocation-index)
- [Aggregation](#aggregation)
- [Working with arrays](#working-with-arrays)
- [Working with Geo-location](#working-with-geo-location)
- [Transactions - Session](#transactions-session)
- [Capped Collections](#capped-collections)
- [Numbers](#numbers)


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
> Creates new database and starts using it if does not exist, or uses already existing database
```js
use blog
```

## Import data to Database
```js
mongoimport datafile.json -d databasename -c collectionname --jsonArray --drop
// --drop drop if exist, --jsonArray array, collectioname desired table name
```

## Drop Database
> Delete database
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

## Insert Document
```js
db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
})
```

## Insert Document with additional parameters
> - Some insert operations takes a considerable amount of time which can exceed the maximum allowed time for this operation. 
> - In order to implement these heavy operations, we have to store them in `Journal` (kind of a secondary memory) where they are kept for later submission.
> - `writeConcern` has `w` (write), `j` (journal), `wtimeout` (time for server to report successful insert)
```js
{writeConcern:{w: 1, j: undefined}} // direct store
{writeConcern:{w: 1, j: true}} // j - storing insert in journal
{writeConcern:{w: 1, j:true, wtimeout: 200}} // wtimeout is 200 ms

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j: undefined}})

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j: true}})

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j:true, wtimeout: 200}})

```

## Insert Multiple Documents
> Documents are stored in the order of the inputs. We can change it by setting the `ordered` parameter to `false` 
```js
db.posts.insertMany([
  {
    title: 'Post 2',
    body: 'Body of post.',
    category: 'Event',
    likes: 2,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 3',
    body: 'Body of post.',
    category: 'Tech',
    likes: 3,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 4',
    body: 'Body of post.',
    category: 'Event',
    likes: 4,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 5',
    body: 'Body of post.',
    category: 'News',
    likes: 5,
    tags: ['news', 'events'],
    date: Date()
  }
], {ordered:false}) // ordered default is true
```

## Find All Documents
> - We can get all the documents by calling `toArray()` method along with `find()` method.
> - `forEach()` method is better than `toArray()` in terms of getting large datasets, because it does not load the data at once.
> - However, it is not a good approach to get all the documents, because it would heavily load the server. Thus `find()` method gets certain amount data with `cursor`.
> - We can get the next chunk of data by calling `next()` method to current cursor. 
```js
db.posts.find() // with cursor in 20th element
db.posts.find().next() // next chunk of documents
db.posts.find().toArray() // all the data
db.posts.find().forEach(data=>{printjson(data)}) // better than toArray() for large datasets, not loading data at once
```

## Find One Document
```js
db.posts.findOne({ likes: 3 }) // getting first document where likes field is equal to 3
db.posts.findOne({ likes: { $gt: 3 } }) // getting first document where likes field is greater than 3
db.posts.findOne({ "rating.average": { $gt: 3 } }) // nested document
db.posts.find({column:"hey"}) // if column is array, then it should include the word "hey"
```

## Find Documents with Query
```js
db.posts.find({ category: 'News' })
db.posts.findOne({ "rating.average": 3 }) // nested document
```

## Sort Documents
> - Setting sorted field to 1 means ascending, 0 means descending order
> - We can sort documents according to multiple fields. Order matters
### Ascending
```js
db.posts.find().sort({ title: 1})
```

### Descending
```js
db.posts.find().sort({ title: -1 })
```

### Multiple field sort
```js
db.posts.find().sort({ title: 1, rating:-1 })
```

## Count Documents
> Total number of documents
```js
db.posts.find().count()
db.posts.find({ category: 'news' }).count() // The number of documents satisfying the query
```

## Limit Documents
> Getting specified number of documents
```js
db.posts.find().limit(2) // getting 2 documents
```

## Chaining
> Methods can be used together. Order is not important
```js
db.posts.find().limit(2).sort({ title: 1 })
```

## Projection
> Selecting specific fields of documents. Field is set to 1 (include field) or 0 (exclude field). Projections of different data structures vary. 
```js
db.posts.find({}, {title:1}) // title:1 means include, title:0 means exclude 'title' column
db.posts.find({}, {"sections.$":1}) // array projection. '$' sign represents the elements of array 
db.posts.find({}, {sections:{$elemMatch:{$eq:"AG1"}}}) // array projection will include 'sections' field if section has "AG1"
db.posts.find({}, {sections:{$slice:[1,2]}}) // skipping 1st element and getting next 2 elements of array
```

## Comparison
> `$gt` - greater than 
> `$gte` - greater than or equal 
> `$lt` - lower than
> `$lte` - lower than or equal
> `$eq` - equal
> `$ne` - not equal
> `$in` - exists in array
> `$nin` - not exists in array
```js
db.posts.findOne({ likes: { $gt: 3 } }) // a document where likes is greater than  3
db.posts.findOne({ likes: { $gte: 3 } }) // a document where likes is greater than or equal to 3
db.posts.findOne({ likes: { $lt: 3 } }) // a document where likes is lower than 3
db.posts.findOne({ likes: { $lte: 3 } }) // a document where likes is lower than or equal to 3
db.posts.findOne({ likes: { $eq: 3 } }) // a document where likes is equal to 3
db.posts.findOne({ likes: { $ne: 3 } }) // a document where likes is not equal to 3
db.posts.findOne({ likes: { $in: [1, 3, 6] } }) // a document where likes is equal to one of the these numbers - 1, 3, 6
db.posts.findOne({ likes: { $nin: [1, 3, 6] } }) // a document where likes is equal to none of the these numbers - 1, 3, 6
```

## Logical Operators
> `AND`, `OR`, `NOR`, `NOT`
```js
db.data.find({ $and:  [{"pressure.value":1029.2}, {"dewPoint.value":-29.8}]} )
db.data.find({ $or:  [{"pressure.value":1029.2}, {"dewPoint.value":-29.8}]} )
db.data.find({ $nor:  [{"pressure.value":1029.2}, {"dewPoint.value":-29.8}]} )

db.data.find({ "pressure.value": {$not: {$gt: 1029.2}}}) // NOT
```

## Evaluation Query
```js
// regex
db.data.find({ type: {$regex: /FM/}})

// expr
```

## Element Query 
> - `$exists` - checks if field exists
> - `$type` - checks if field data type is the given BSON type (number, float, string ...)
```js
db.data.findOne({ "pressure.value":{$exists:true, $gt:1030}})
db.data.findOne({ "pressure.value":{$type:"number"}})
```

## Array
```js
// $all - finds data equal to exact array, but order of array elements are not important
db.data.find({ "sections":{$all:["abc","abd"]}})
db.posts.find().forEach(data=>{printjson(data)}) // better than toArray() for large datasets, not loading data at once
```

## Cursor
> - `next()` - next chunk of data
> - `hasNext()` - checks if data has next cursor
```js
const data = db.data.find()
data.next()
data.hasNext() // checks if data has next cursor
```

## Skip and Limit
```js
db.data.find().skip(100).limit(10) // skip first 100 documents and get next 10 documents
```

## Update Document
> `Sset` parameter is used to update fields.
```js
db.posts.updateOne({ title: 'Post 1' },
{
  $set: {
    category: 'Tech'
  }
})
```

## Update Document or Insert if does not exist
> `upsert` parameter is used to update existing document or insert new document if does not exist.
```js
db.posts.updateOne({ title: 'Post 6' },
{
  $set: {
    title: 'Post 6',
    body: 'Body of post.',
    category: 'News'
  }
},
{
  upsert: true
})
```

## Increment Field 
> - `$inc` - incerement field by given input.
> - `$mul` - multiplies field with given input.
```js
db.posts.updateOne({ title: 'Post 1' }, { $inc: { likes: 2 // incerement likes by 2 }})
db.posts.updateOne({ title: 'Post 1' },{ $mul: { likes: 2 }}) // multiple likes by 2
```

## Min - Max
> - `$min` - sets field to new value if new value is lower than original value.
> - `$max` - sets field to new value if new value is greater than original value.
```js
db.posts.updateOne({ title: 'Post 1' },{ $min: { likes: 2 }}) // sets likes to 2 if likes is greater than 2
db.posts.updateOne({ title: 'Post 1' },{ $max: { likes: 2 }}) // sets likes to 2 if likes is lower than 2
```

## Getting rid of field
> `$unset`- Removes field from document if field is equal to given input
```js
db.posts.updateMany({}, { $unset: { likes: ""} })
```

## Update Multiple Documents
```js
db.posts.updateMany({}, {
  $inc: {
    likes: 1
  }
})
```

## Rename field
```js
db.posts.updateMany({}, { $rename: { rating: "totalRating"} })
```

## Updating Arrays
> - `arrayFilters` - filters elements (nested documents) of array that satisfy the query.
> - `$push` - add element to array (possible element duplicates).
> - `$addToSet` - add distinct elment, no dublicate.
> - `$pull` - remove element from array.
> - `$pop` - remove last (1) or first (-1) element of array.
```js
// updating exactly one column
db.posts.updateMany({
  sections:{$elemMatch: {title:"title 1", rating:{$gte:3}}}
},
{
  $set: {
    "sections.$.newField": true 
  }
})

// updating specific elements of array
db.posts.updateMany({},
  { $set: { "sections.$[el].newField": true }}, 
  { arrayFilters: [{"el.frequency":{$gt:2}}] }) 

// adding element to array, data - {...} (Object) 
db.posts.updateOne({}, { $push: { sections: {...} } }), 
db.posts.updateOne({}, { $push: { sections: { $each:[{...}, {...}], $sort:{column:1}}} }) 
db.posts.updateOne({}, { $addToSet: { sections: {...} }) // adds distinct elment, no dublicate

// remove element from array
db.posts.updateOne({}, { $pull: { sections: { column:"value" } } })
db.posts.updateOne({}, { $pop: { sections: 1 } } ) // remove last (1) or first (-1) element
```

## Delete a Document
```js
db.posts.deleteOne({ title: 'Post 6' })
```

## Delete Multiple Documents
```js
db.posts.deleteMany({ category: 'Tech' })
```

## Delete all documents
```js
db.posts.drop()
```

## Relations 
> `$lookup` - parameter is used to setup the relationed data
> `from` - collection name we want to call
> `localField` - reference field name
> `foreignField` - foreign key
> `as` - alias for resulting document (relationed data)
```js
db.posts.aggregate([{ 
  $lookup:{
    from: "authors", // from the tablename
    localField:"author", // column of reference
    foreignField: "_id", // foreign key
    as:"author", // alias for resultin column
  }
}])

// Example posts - author (One to Many) & post - users - comments (Many to Many)
{
  _id: ObjectId("6230386bfb78d9a361b7edc4"),
  title: 'Post 5 new',
  body: 'Body of post.',
  category: 'News',
  likes: 8,
  tags: [ 'news', 'events' ],
  date: 'Tue Mar 15 2022 10:55:39 GMT+0400 (Azerbaijan Standard Time)',
  author: ObjectId("6235d11e9ec2e26c0c29752c"),
  comments: [
    { user: ObjectId("6235d11e9ec2e26c0c29752c"), text: 'text1' },
    { user: ObjectId("6235d11e9ec2e26c0c29752c"), text: 'text1' }
  ]
}
```

## Query Diagnosis and Query Planning
> For performance Look at:  
> - Milliseconds Proccess Time
> - IXSCAN vs COLLSCAN
> - number of Keys Examined
> - number of Documents Examined
> - number of Documents Returned  
> - keys and examined docs should be close, examined and returned docs should be close
```js
// Statistics
db.data.stats()

// Details of query
db.data.explain().find()

// Execution details - time, success etc.
db.data.explain('executionStats').find()

// Execution details of all indexes.
db.data.explain('allPlansExecution').find()
```

## Indexes
> For getting minority (lower than 20-30% of all data), indexes are useful,  but for getting majority of data indexes are slower. 
> For very large data Collection Scan (default) is better than Index scan. 
> Features:
> - Reducing time
> - Useful for sorting (collection scan will timeout if data is large)

## Create Index
```js
// Create Index
db.data.createIndex({"airTemperature.value":1}) // Sort ascending

// Configuring Indexes
db.data.createIndex({"st":1}, {unique:true}) // Unique index, no duplicate will be allowed

// Compound Index - Merging fields in one index
db.data.createIndex({"airTemperature.value":1, type:"FM-13"})

// Applying unique index for documents where specified column exists
db.data.createIndex({"st":1}, {unique:true, partialFilterExpression:{st:{$exists:true}}})

// Time to live index - Specifing expiration time for documents, works only with 'date' objects and has to be single index
db.data.createIndex({"created_at":1}, {expireAfterSeconds:1000}) // document will be deleted after 1000 seconds
```

## Partial Index
> Create index for documents only meeting specified query. `Compound index` size is smaller than this `Partial index`
> `partialFilterExpression` - make query
```js
// Apply index to documents having "type" column equal to "FM-13" 
db.data.createIndex({"airTemperature.value":1}, {partialFilterExpression:{type:"FM-13"}}) 
// Making query for partial index. 
db.data.find({"airTemperature.value":18, type:"FM-13"}) 
```

## Text Index
> Index for text search
```js
// Text Index
db.data.createIndex({callLetters:"text"})
db.data.find({$text:{$search:"search value"}}) // "search" and "value" treated seperately
db.data.find({$text:{$search:"\"search value\""}}) // "search" and "value" treated together
db.data.find({$text:{$search:"\"search value\""}}).sort({score:{$meta:"textScore"}}) // sorting by text search result relevancy

// Combined text search
db.data.createIndex({callLetters:"text", type:"text"})

// Exclude some words. Putting - in front of the word
db.data.find({$text:{$search:"-value"}})

// Language settings and weights
// Language is german and "callLetters" is 10 times important than "type"
db.data.createIndex({callLetters:"text", type:"text"}, {default_language:"german", weights:{type:1, callLetters:10}})
db.data.find({$text:{$search:"value", $caseSensitive:true, $language:"turkish"}}) // case sensitive and turkish search
```

## Get Indexes
> `getIndexes()` - It will always show default index (index for ID) along with other indexes
```js
// View all indexes 
db.data.getIndexes()
```

## Delete Index
```js
// Delete Index
db.data.dropIndex({"airTemperature.value":1})
db.data.dropIndex("airTemperature.value_1") // delete by Index name
```

## Forefround Index vs Background Index
> Adding Index in Background. Foreground Indexing locks the collection (not able to query database) until Indexing is completed. 
> Background Index is useful production, as it does not lock the collection
```js
db.data.createIndex({"airTemperature.value":1}, {background:true})
```

## Geolocation index
```js
// Geolocation index
db.data.createIndex({position:"2dsphere"})
```

## Aggregation
> Merging the methods together in a pipeline. Aggregation functions are executed one after another, so the order matters.
> Aggregation methods:
> - `$match` - finding the documents matching the field input.
> - `$group` - grouping the documents by the field (field should be set as `_id` parameter). 
> The second parameter is the additional data (sum, count, average etc) derived from grouped documents.
> - `$sort` - sorting the documents.
> - `$project` - selecting the specified fields.
> - `$skip` - skip documents.
> - `$limit` - get the specified number of documents.
> - `$out` - write the resulting documents into a new collection.
> - `$bucket` - group the documents into seperate chunks according to the specified boundaries. 
> `groupBy` is field, `boundaries` is an array of elements that field value is between them. `output` is output of resulting chunk. 
> `default` is default value is field is not between `boundaries` elements.
```js
db.data.aggregate([
  { $match: { type:"FM-13"} },
  { $group: { _id: {temperature: "$airTemperature.value"}, totalTemperature: {$sum: 1} } },
  { $sort: { totalTemperature: -1} }
])

db.data.aggregate([
  { $match: { type: "FM-13" } },
  { 
    $project: {
    st:{ $concat: ["$st", " ", "$type"]},
    date:{ $toDate:"$ts" }
  }},
  { $sort: { date:1 } },
  { $skip: 10 },
  { $limit: 10 }
])

// Bucketing
db.data.aggregate([
  { 
    $bucket: { 
      groupBy: "$airTemperature.value",
      boundaries:[0, 7, 9, 20],
      output:{
        avgTemperature:{ $avg: "$airTemperature.value"},
      },
      default:-100
    } 
  }
])

db.data.aggregate([
  { 
    $bucketAuto: { 
      groupBy: "$airTemperature.value",
      buckets:5,
      output:{
        avgTemperature:{ $avg: "$airTemperature.value"},
      }
    } 
  }
])
```

## Working with arrays
> - `$unwind` destructures the document having the array of n number elements into n number documents having single element.
> {title:'Title', arr:[1, 2, 3]} is  converted to {title:'Title', arr:1}, {title:'Title', arr:2}, {title:'Title', arr:3}
> - `$filter` filters the array according to the given condition. `input` is input array, `cond` is condition, `as` is field name for result.
```js
// Arrays
db.data.aggregate([
  { $group: { _id: {temperature: "$airTemperature.value"}, allSections: {$push: "$sections"} } },
])

db.data.aggregate([
  { $unwind: "$sections"},
  { $group: { _id: {temperature: "$airTemperature.value"}, allSections: {$addToSet: "$sections"} } },
])

db.data.aggregate([
  { 
    $project: { 
      sections: { $slice: ["$sections", 2, 3]},
      sectionsCount: { $size:"$sections" } // return size of array (the number of elements)
    },
  }
])

db.restaurants.aggregate([
  { 
    $project: { 
      grades: { $filter: { input:"$grades", as:"grade", cond:{$gt:["$$grade.score", 10]}}},
    },
  }
])

db.restaurants.aggregate([
  { $unwind: "$grades" },
  { $project: { score: "$grades.score",  borough:1}},
  { $sort: { "grades": 1 }},
  { $group: { _id: "$_id", borough:{ $first: "$borough" } ,maxScore:{$max:"$score"} }},
  { $sort: { maxScore:-1 }}
])
```

## Working with Geo-location
```js
// Geo data
db.data.createIndex({position:"2dsphere"})
db.data.aggregate([
  { 
    $geoNear: { 
      near:{ type:"Point", coordinates:[-171, 79]},
      maxDistance: 100000,
      query: { type:"FM-13" },
      distanceField: "geo_distance"
    },
  },
  {$limit:10}
])

// Geo-location as array. '$arrayElemAt' is used to get element of array at the given index (arr[0], arr[1] etc.)
// '$convert' is used to convert data to specified data type (integer, double etc.)
db.data.aggregate([
  { $project: { 
    ts:1, 
    st:1,
    type:1,
    position:{
      type:"Point", 
      coordinates:[
        {$convert: {input:{$arrayElemAt: [ "$position.coordinates", 0 ]}, to:"double", onError:0, onNull:0}}, 
        {$convert: {input:{$arrayElemAt: [ "$position.coordinates", 1 ]}, to:"double", onError:0, onNull:0}}, 
      ]
    },
    date:{ $toDate:"$ts" }
  }},
  { $project: {
    position:1,
    date:1,
    name: {
      $concat:[
        { $toUpper: {$substrCP:["$st", 0, 1]}}, 
        { $substrCP:["$st", 1, {$subtract:[{$strLenCP:"$st"},1]} ]}, 
        " ", 
        "$type"
      ]}} 
  },
  { $out: "transformedWeatherData" } // write to new collection
])
```

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

## Capped Collections
> - `{capped:true, size: 10000, max: 5}` - `capped` sets the collection as capped, `size` - maximum size of collection in bytes, `max` - maximum number of document
```js
// Creating capped collection with 10000 bytes size, and maximum 5 documents
db.createCollection("capped", {capped:true, size: 10000, max: 5})
```

## Numbers
> - `NumberInt` - Int(32), 
> - `NumberLong` - Int(64), 
> - `NumberDecimal` - Double(128)
```js
db.data.insertOne({age:NumberInt("29")}) // Inserting int(32)
db.data.insertOne({age:NumberLong("29")}) // Inserting int(64)
db.data.insertOne({price:34.23}) // Inserting double
db.data.insertOne({price:NumberDecimal("29.12")}) // Inserting double(128)
```
