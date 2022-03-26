# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 8 - Indexes

## Table of Contents
- [Indexes](#indexes)
- [Create Index](#create-index)
- [Partial Index](#partial-index)
- [Text Index](#text-index)
- [Get Indexes](#get-index)
- [Delete Index](#delete-index)
- [Forefround Index vs Background Index](#forefround-index-vs-background-index)
- [Geolocation index](#geolocation-index)


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
