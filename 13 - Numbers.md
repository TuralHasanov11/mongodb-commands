# MongoDB Cheat Sheet

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 13 - Numbers

## Table of Contents
- [Numbers](#numbers)


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
