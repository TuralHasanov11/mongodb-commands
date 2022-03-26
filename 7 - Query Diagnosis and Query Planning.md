# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 7 - Query Diagnosis and Query Planning

## Table of Contents
- [Query Diagnosis and Query Planning](#uery-diagnosis-and-query-planning)


## Query Diagnosis and Query Planning
> For performance Look at:  
> - Milliseconds Proccess Time
> - IXSCAN vs COLLSCAN
> - number of Keys Examined
> - number of Documents Examined
> - number of Documents Returned  
> - keys and examined docs should be close, examined and returned docs should be close
```js
// Statistics
db.data.stats()

// Details of query
db.data.explain().find()

// Execution details - time, success etc.
db.data.explain('executionStats').find()

// Execution details of all indexes.
db.data.explain('allPlansExecution').find()
```
