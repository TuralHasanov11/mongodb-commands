# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 6 - Database and Collection operations

## Table of Contents
- [Relations](#relations)


## Relations 
> `$lookup` - parameter is used to setup the relationed data
> `from` - collection name we want to call
> `localField` - reference field name
> `foreignField` - foreign key
> `as` - alias for resulting document (relationed data)
```js
db.posts.aggregate([{ 
  $lookup:{
    from: "authors", // from the tablename
    localField:"author", // column of reference
    foreignField: "_id", // foreign key
    as:"author", // alias for resultin column
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

