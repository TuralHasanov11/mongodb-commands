# MongoDB Cheat Sheet 2022

> Follow my channel

## Table of Contents
- [Check `monosh` Version](#check-monosh-version)
- [Start the Mongo Shell](#start-the-mongo-shell)
- [Show Current Database](#show-current-database)
- [Show All Databases](#show-all-databases)
- [Create Or Switch Database](#create-or-switch-database)
- [Import data to Database](#import-data-to-database)
- [Drop Database](#drop-database)
- [Create Collection](#create-collection)
- [Show Collections](#show-collections)
- [Insert Document](#insert-document)
- [Insert Multiple Documents](#insert-multiple-documents)
- [Find All Documents](#find-all-documents)
- [Find Documents with Query](#find-documents-with-query)
- [Sort Documents](#sort-documents)
- [Count Documents](#count-documents)
- [Limit Documents](#limit-documents)
- [Chaining](#chaining)
- [Find One Document](#find-one-document)
- [Update Document](#update-document)
- [Update Document or Insert if not Found](#update-document-or-insert-if-not-found)
- [Increment Field (`$inc`)](#increment-field-inc)
- [Update Multiple Documents](#update-multiple-documents)
- [Rename Field](#rename-field)
- [Delete a Document](#delete-a-document)
- [Delete Multiple Documents](#delete-multiple-documents)
- [Greater & Less Than](#greater--less-than)
- [Relations](#relations)

## Check `monosh` Version

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

```js
use blog
```

## Drop Database

```js
db.dropDatabase()
```

## Import data to Database

```js
mongoimport datafile.json -d databasename -c collectionname --jsonArray --drop
// --drop drop if exist, --jsonArray array, collectioname desired table name
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

// Additional Parameters
db.posts.insertOne({...}, params) // additional parameters below
{writeConcern:{w: 1, j: undefined}} // direct store
{writeConcern:{w: 1, j: true}} // j - storing insert in journal (tasks kept for later)
{writeConcern:{w: 1, j:true, wtimeout: 200}} // wtimeout - time for server to report successful insert
```

## Insert Multiple Documents

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

```js
db.posts.find() // with cursor in 20th element
db.posts.find().toArray() // all the data
db.posts.find().forEach(data=>{printjson(data)}) // better than toArray() for large datasets, not loading data at once
```

## Projection - Selecting specific columns

```js
db.posts.find({}, {title:1}) // {title:1} means include, {title:0} means exclude 'title' column
db.posts.find({}, {"sections.$":1}) // array projection
db.posts.find({}, {sections:{$elemMatch:{$eq:"AG1"}}}) // array projection will include 'sections' field if section has "AG1"
db.posts.find({}, {sections:{$slice:[1,2]}}) // skipping 1st element and getting 2 elements of array

```

## Find Documents with Query

```js
db.posts.find({ category: 'News' })
```

## Sort Documents

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

```js
db.posts.find().count()
db.posts.find({ category: 'news' }).count()
```

## Limit Documents

```js
db.posts.find().limit(2)
```

## Chaining

```js
db.posts.find().limit(2).sort({ title: 1 })
```

## Find One Document

```js
db.posts.findOne({ likes: { $gt: 3 } })
db.posts.findOne({ "rating.average": { $gt: 3 } }) // nested document
// $gt, $gte, $lt, $lte, $eq, $ne, $in, $nin

db.posts.find({column:"hey"}) // if column is array, then it should include the word "hey", not exactly equal to "hey"
```

## Logical Operators
```js
// AND, OR, NOR
db.data.find({ $or:  [{"pressure.value":1029.2}, {"dewPoint.value":-29.8}]} )

// NOT
db.data.find({ "pressure.value": {$not: {$gt: 1029.2}}})
```

## Evaluation Query
```js
// regex
db.data.find({ type: {$regex: /FM/}})

// expr
```

## Element Query 
```js
// $exists - checks if field exists
db.data.findOne({ "pressure.value":{$exists:true, $gt:1030}})

// $type - checks if field data type is the given BSON type (number, float, string ...)
db.data.findOne({ "pressure.value":{$type:"number"}})
```

## Array
```js
// $all - finds data equal to exact array, but order of array elements are not important
db.data.find({ "sections":{$all:["abc","abd"]}})
db.posts.find().forEach(data=>{printjson(data)}) // better than toArray() for large datasets, not loading data at once
```

## Cursor
```js
const data = db.data.find()
data.next()
data.hasNext() // checks if data has next cursor
```

## Skip and Limit
```js
db.data.find().skip(100).limit(10) // skip first 100 documents (rows) and get 10 documents
```

## Update Document

```js
db.posts.updateOne({ title: 'Post 1' },
{
  $set: {
    category: 'Tech'
  }
})
```

## Update Document or Insert if not Found

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

## Increment Field (`$inc`)

```js
db.posts.updateOne({ title: 'Post 1' },
{
  $inc: {
    likes: 2
  }
})
```

## Min- Max - Mul Field (`$min`, `$max`, `$mul`)

```js
// $min sets field to new value if new value is lower than original value
// $max sets field to new value if new value is greater than original value
// $mul multiplies field with given input

db.posts.updateOne({ title: 'Post 1' },{ $min: { likes: 2 }})
db.posts.updateOne({ title: 'Post 1' },{ $max: { likes: 2 }})
db.posts.updateOne({ title: 'Post 1' },{ $mul: { likes: 2 }})
```

## Getting rid of field

```js
db.posts.updateMany({}, { $unset: { likes: ""} }) // removes field from document if field is equal to given input
```

## Rename field

```js
db.posts.updateMany({}, { $rename: { rating: "totalRating"} })
```

## Update Multiple Documents

```js
db.posts.updateMany({}, {
  $inc: {
    likes: 1
  }
})
```

## Rename Field

```js
db.posts.updateOne({ title: 'Post 2' },
{
  $rename: {
    likes: 'views'
  }
})
```

## Updating Arrays

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

## Greater & Less Than

```js
db.posts.find({ views: { $gt: 2 } })
db.posts.find({ views: { $gte: 7 } })
db.posts.find({ views: { $lt: 7 } })
db.posts.find({ views: { $lte: 7 } })
```

## Data Types
```
Text: 'Text'
Boolean
Number -> Integer(int32), NumberLong(int64), NumberDecimal
ObjectId("asddad")
ISODate("2022-02-02")
Timestamp(11212123)
Embedded Document / Object: {"a":{...}}
Array: {"b":[...]} 
```

## Relations 

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
```js
// Details of query
db.data.explain().find()

// Execution details - time, success etc.
db.data.explain('executionStats').find()

// Execution details of all indexes.
db.data.explain('allPlansExecution').find()

/* Look at:  
  Milliseconds Proccess Time
  IXSCAN vs COLLSCAN
  # of Keys Examined
  # of Documents Examined
  # of Documents Returned
  
  keys and examined docs should be close, examined and returned docs should be close
 */
```

## Indexes
```js
/* ### For getting minority (lower than 20-30% of all data), indexes are useful, 
but for getting majority of data indexes are slower. 
For very large data Collection Scan (default) is better than Index scan. 

Features:
  1. Reducing time
  2. Useful for sorting (collection scan will timeout if data is large)
### */

// Create Index
db.data.createIndex({"airTemperature.value":1}) // Sort ascending

// Delete Index
db.data.dropIndex({"airTemperature.value":1})
db.data.dropIndex("airTemperature.value_1") // delete by Index name

// Compound Index - Merging 
db.data.dropIndex({"airTemperature.value":1, type:"FM-13"})

// View all indexes 
db.data.getIndexes() // It will always show default index (index for ID) along with other indexes

// Configuring Indexes
db.data.createIndex({"st":1}, {unique:true}) // Unique index, no duplicate will be allowed

// Partial Index - Create index for documents only meeting specified standard
// Apply index to documents having "type" column equal to "FM-13" 
db.data.createIndex({"airTemperature.value":1}, {partialFilterExpression:{type:"FM-13"}}) 
// Making query for partial index. Compound index size is smaller than this Partial index
db.data.find({"airTemperature.value":18, type:"FM-13"}) 

// Applying unique index for documents where specified column exists
db.data.createIndex({"st":1}, {unique:true, partialFilterExpression:{st:{$exists:true}}})

// Time to live index - Specifing expiration time for documents, works only with 'date' objects and has to be single index
db.data.createIndex({"created_at":1}, {expireAfterSeconds:1000}) // document will be deleted after 1000 seconds

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


/* Adding Index in Background. 
  Foreground Indexing locks the collection (not able to query database) until Indexing is completed. 
  Background Index is useful production, as it does not lock the collection
*/
db.data.createIndex({"airTemperature.value":1}, {background:true})
```

## Geospatial Data
```js
// long: 41.021091025785985, lat: 29.00475194715994
db.data.insertOne({location:{type:"Point", coordinates:[41.021091025785985, 29.00475194715994]}})
db.data.createIndex({location:"2dsphere"}) // create index for spotting geo data
db.data.find({location:{ $near: { $geometry: {type:"Point", coordinates:[41.021091025785985,29.00475194715994]}, $maxDistance:30, $minDistance:10}}}) // limiting the radius of area with max and min distances

// Get all data within the radius of location. Difference from $near is that near does not sort data
db.data.find({location:{ $geoWithin: { $centerSphere: [[41.021091025785985,29.00475194715994], 1/6378.1]}}}) // radius 1 km

// Finding documents having location within the area specified coordinates
db.data.find({location:{ $geoWithin: { $geometry: {type:"Polygon", coordinates:[[
  [41.021166, 29.003985],
  [41.021021, 29.004008],
  [41.021199, 29.004230],
  [41.021043, 29.004255],
  [41.021166, 29.003985]
 ]]}}}}) 
 
 // Storing area
 db.data.inserOne({location:{ $geoWithin: { $geometry: {type:"Polygon", coordinates:[[
  [41.021166, 29.003985],
  [41.021021, 29.004008],
  [41.021199, 29.004230],
  [41.021043, 29.004255],
  [41.021166, 29.003985]
 ]]}}}}) 
 
db.data.createIndex({area:"2dsphere"}) // create index for spotting area
// Finding doc having the coordinates intersecting with the area
db.data.find({area:{$geoIntersects:{$geometry:{type:"Point", coordinates:[41.021091025785985, 29.00475194715994]}}}})
```

## Aggregation
```js
db.data.aggregate([
  { $match: { type:"FM-13"} },
  { $group: { _id: {temperature: "$airTemperature.value"}, totalTemperature: {$sum: 1} } },
  { $sort: { totalTemperature: -1} }
])

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
])

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
      sectionsCount: { $size:"$sections" }
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

