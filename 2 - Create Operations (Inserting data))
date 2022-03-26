# MongoDB Cheat Sheet 2022

> Follow my channel - https://www.youtube.com/channel/UCvE-1xbTsOH_Kstd8PJdiXQ

## 2 - Create Operations (Inserting data))

## Table of Contents
- [Insert Document](#insert-document)
- [Insert Document with additional parameters](#insert-document-with-additional-parameters)
- [Insert Multiple Documents](#insert-multiple-documents)


## Insert Document
```js
db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
})
```

## Insert Document with additional parameters
> Some insert operations takes a considerable amount of time which can exceed the maximum allowed time for this operation. 
> In order to implement these heavy operations, we have to store them in `Journal` (kind of a secondary memory) where they are kept for later submission.
> `writeConcern` has `w` (write), `j` (journal), `wtimeout` (time for server to report successful insert)
```js
{writeConcern:{w: 1, j: undefined}} // direct store
{writeConcern:{w: 1, j: true}} // j - storing insert in journal
{writeConcern:{w: 1, j:true, wtimeout: 200}} // wtimeout is 200 ms

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j: undefined}})

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j: true}})

db.posts.insertOne({
  title: 'Post 1',
  body: 'Body of post.',
  category: 'News',
  likes: 1,
  tags: ['news', 'events'],
  date: Date()
}, {writeConcern:{w: 1, j:true, wtimeout: 200}})

```

## Insert Multiple Documents
> Documents are stored in the order of the inputs. We can change it by setting the `ordered` parameter to `false` 
```js
db.posts.insertMany([
  {
    title: 'Post 2',
    body: 'Body of post.',
    category: 'Event',
    likes: 2,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 3',
    body: 'Body of post.',
    category: 'Tech',
    likes: 3,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 4',
    body: 'Body of post.',
    category: 'Event',
    likes: 4,
    tags: ['news', 'events'],
    date: Date()
  },
  {
    title: 'Post 5',
    body: 'Body of post.',
    category: 'News',
    likes: 5,
    tags: ['news', 'events'],
    date: Date()
  }
], {ordered:false}) // ordered default is true
```
