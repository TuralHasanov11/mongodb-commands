# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 5 - Delete Operations (Deleting Documents)

## Table of Contents
- [Delete a Document](#delete-a-document)
- [Delete Multiple Documents](#delete-multiple-documents)


## Delete a Document
```js
db.posts.deleteOne({ title: 'Post 6' })
```

## Delete Multiple Documents
```js
db.posts.deleteMany({ category: 'Tech' })
```

## Delete all documents
```js
db.posts.drop()
```



