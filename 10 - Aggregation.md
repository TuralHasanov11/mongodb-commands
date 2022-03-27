# MongoDB Cheat Sheet

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 10 -  Aggregation

## Table of Contents
- [Aggregation](#aggregation)
- [Working with arrays](#working-with-arrays)
- [Working with Geo-location](#working-with-geo-location)


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
