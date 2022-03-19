# MongoDB Cheat Sheet 2022

> MongoDB Crash Course 2022 < TODO: Add Video Link

## Table of Contents
- [Check `monosh` Version](#check-monosh-version)
- [Start the Mongo Shell](#start-the-mongo-shell)
- [Show Current Database](#show-current-database)
- [Show All Databases](#show-all-databases)
- [Create Or Switch Database](#create-or-switch-database)
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
])
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
```

## Find Documents with Query

```js
db.posts.find({ category: 'News' })
```

## Sort Documents

### Ascending

```js
db.posts.find().sort({ title: 1 })
```

### Descending

```js
db.posts.find().sort({ title: -1 })
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
    localField:"authors", // column of reference
    foreignField: "_id", // foreign key
    as:"authors", // alias for resultin column
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
