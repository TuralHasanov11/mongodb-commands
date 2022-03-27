# MongoDB Cheat Sheet

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 12 - Capped Collections

## Table of Contents
- [Capped Collections](#capped-collections)


## Capped Collections
> - `{capped:true, size: 10000, max: 5}` - `capped` sets the collection as capped, `size` - maximum size of collection in bytes, `max` - maximum number of document
```js
// Creating capped collection with 10000 bytes size, and maximum 5 documents
db.createCollection("capped", {capped:true, size: 10000, max: 5})
```
