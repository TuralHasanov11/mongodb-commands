# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 3 - Database and Collection operations

## Table of Contents
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
