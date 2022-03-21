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
db.posts.updateMany({
  sections:{$elemMatch: {title:"title 1", rating:{$gte:3}}}
},
{
  $set: {
    "sections.$.newField": true // updating exactly one column
  }
})
```

## Delete a Document

```js
db.posts.deleteOne({ title: 'Post 6' })
```

## Delete Multiple Documents

```js
db.posts.deleteMany({ category: 'Tech' })
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
