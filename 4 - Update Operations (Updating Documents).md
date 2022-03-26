# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 4 - Update Operations (Updating Documents)

## Table of Contents
- [Update Document](#update-document)
- [Update Document or Insert if not Found](#update-document-or-insert-if-not-found)
- [Increment Field (`$inc`)](#increment-field-inc)
- [Min - Max](#min-max)
- [Getting rid of field](#getting-rid-of-field)
- [Update Multiple Documents](#update-multiple-documents)
- [Rename Field](#rename-field)
- [Updating Arrays](#updating-arrays)


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
> `$inc` - incerement field by given input.
> `$mul` - multiplies field with given input.
```js
db.posts.updateOne({ title: 'Post 1' }, { $inc: { likes: 2 // incerement likes by 2 }})
db.posts.updateOne({ title: 'Post 1' },{ $mul: { likes: 2 }}) // multiple likes by 2
```

## Min - Max
> `$min` - sets field to new value if new value is lower than original value.
> `$max` - sets field to new value if new value is greater than original value.
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
> `arrayFilters` - filters elements (nested documents) of array that satisfy the query.
> `$push` - add element to array (possible element duplicates).
> `$addToSet` - add distinct elment, no dublicate.
> `$pull` - remove element from array.
> `$pop` - remove last (1) or first (-1) element of array.
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
