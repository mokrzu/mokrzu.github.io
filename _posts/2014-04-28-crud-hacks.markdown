---
layout: post
title:  "CRUD hacks"
date:   2014-04-28 16:06:19
categories: mongodb
image: http://www.fillmurray.com/300/200
image_src: http://www.fillmurray.com
---

##### CREATE

First of all, **insert** isn't the only method, that could create new documents.
<br>
Performing **update** operation with, **upsert** flag on, would create new entry,
when none of current documents match the query.

```js
> db.tools.update({ name: "rails" }, { $set: { type: "framework" } }, { upsert: true })
```
<br>

MongoDb alows creating multipe documents in single insert call. Just pass array with objects.

```js
> db.tools.insert(
    [
      { name: "mongo", type: "db" },
      { name: "couch", type: "db" }
    ]
  )
```
<br>
##### READ

<br>
##### UPDATE

At the beginnig, updating records in mongodb could by a litle bit confusing.
<br>
Let's look at example of **update** operation:

```js
> db.tools.insert({ name: "mongo", type: "db" })
> db.tools.update({ name: "mongo" }, { type: "nosql solution" })
```
<br>
It swaps whole document content... but what if our intention, was
to just update type field?
<br>
To do so, we need to use **$set** method, as second parameter.

```js
> db.tools.update({ name: "mongo" }, { $set: { type: "nosql solution" } })
```
<br>
This operation updates first matching document, in order to perform that change
for all documents, we need to set **multi** oprtion to **true**.

```js
> db.tools.update({ name: "mongo", { $set: { type: "nosql db" } }, { multi: true })
```
<br>
##### DELETE

The only thing, worth to be mentioned here, is that remove operation by default
deletes **ALL** documents that match given query.

```js
> db.tools.remove({ name: "mongo" })
> // booooom all matching data is lost!
```
<br>
Delete of single document, could be easly done with **justOne** flag.

```js
> db.tools.remove({ name: "mongo" }, { justOne: true })
```
<br>
If we have sharded collection and want to use **justOne** flag,
we need to specify shard key.
<br>
We'll take a closer look at this kind of operations in
*Shards* article.