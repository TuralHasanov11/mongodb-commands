# MongoDB Cheat Sheet

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## Table of Contents
- [Geospatial Data](#geospatial-data)
- [Insert Location](#insert-location)
- [Index Location](#index-location)
- [Find Document according to Location](#find-document-according-to-location)


## Geospatial Data
> We use Longitude and Latitude values to roam around.

## Insert Location
> - To insert geo-location as `point`, we have to set `type` parameter to `Point`. 
> - To insert geo-location as `area`, we have to use `$geoWithin` object and set `type` parameter to `Polygon`.
> - Set the `coordinates` to array of longitude and latitude values.
```js
// long: 41.021091025785985, lat: 29.00475194715994
db.data.insertOne({location:{type:"Point", coordinates:[41.021091025785985, 29.00475194715994]}})

 // Storing area
 db.data.inserOne({location:{ $geoWithin: { $geometry: {type:"Polygon", coordinates:[[
  [41.021166, 29.003985],
  [41.021021, 29.004008],
  [41.021199, 29.004230],
  [41.021043, 29.004255],
  [41.021166, 29.003985]
 ]]}}}}) 
```

## Index Location
```js
db.data.createIndex({location:"2dsphere"}) // create index for spotting point
```

## Find Document according to Location
### Documents near the specific point
> - `$near` parameter used to setup the area of location.
> - `$maxDistance` and `$minDistance` are used to setup maximum and minimum boundaries. 
```js
db.data.find({
  location:{ 
    $near: { 
      $geometry: {type:"Point", coordinates:[41.021091025785985,29.00475194715994]}, 
      $maxDistance:30, 
      $minDistance:10
    }
  }
}) // limiting the radius of area with max and min distances
```
### Documents within the area
> - `$geoWithin` gets all data within the radius of location and sorts it according to closeness to center
> - Difference from `$near` is that near does not sort data
```js
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
```
### Documents intersecting the area
```js
// Finding doc having the coordinates intersecting with the area
db.data.find({area:{$geoIntersects:{$geometry:{type:"Point", coordinates:[41.021091025785985, 29.00475194715994]}}}})
```
