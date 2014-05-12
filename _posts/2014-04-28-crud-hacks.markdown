---
layout: post
title:  "mongodb CRUD hacks"
date:   2014-04-28 16:06:19
categories: mongodb
image: http://placehold.it/300x200&text=mongodb+CRUD+hacks
image_src: http://placehold.it/
---

A few years ago, when I was starting working with both SQL and NoSQL database systems, basic Mongo operations were for me much more intuitive than understanding and using SQL statements.
<br>
But there are several facts and exceptions, you must to remember, to avoid common pitfalls.


##### **CREATE**

First of all, **insert** isn't the only method, that could create new documents.
<br>
Performing **update** operation with, **upsert** flag on, would create new entry,
when none of current documents match the query.

```js
> db.tools.update({ name: "rails" }, { $set: { type: "framework" } }, { upsert: true })
```
<br>

**Save** method could perform insert or update with *upsert* in single call, but only if *_id* field is specified.
<br>
But IMHO it's better to use **update**, because **save** force specifying all field for updated document.

```js
> db.tools.insert({ _id: 42, name: "postgres", type: "db" })
>
> db.tools.save({ name: "postgres", type: "sql-db" })
> // inserts new document with uniqe _id
>
> db.tools.save({ _id: 42, type: "db" })
> // updates record with id 42
> // warning: name field will be lost!
>
> db.tools.save({ _id: 0, type: "db" })
> // there's no record with id 0, so new document will be created
```
<br>
MongoDB alows creating multipe documents in single insert call. Just pass array with objects.

```js
> db.tools.insert(
    [
      { name: "mongo", type: "db" },
      { name: "couch", type: "db" }
    ]
  )
```
<br>
##### **READ**

**Find** could be more powerful than it looks like at the beginning.
<br>
Every query in MongoDb starts from specifing collection for search. It's common for find method,
map/reduce and aggregation framework.
<br>
In **find** method requires providing query criteria and returns [Cursor][cursors] as a result, by default mongo shell prints first 20 objects from that cursor.
<br>
We could apply modifiers...

```js
> db.tools.find({ name: "clojure" }).limit(42)
```
<br>
... or projection filter on results...

```js
> db.tools.find({ name: "clojure" }, { name: 1, type: 1, _id: 0 })
```
<br>
... or use query operators.

```js
> db.tools.find({ name: "clojure", version: { $gt: 2.0 })
>
> db.tools.find({ name: "jave", version: { $lt: 6.0 }}).sort({ version: -1 })
>
> db.tools.find({ $or: [ { version: { $gt: 1.0 } }, { status: 'active' } ] })
```
<br>
You should definitely take a look at rest of [query operators][query-operators].
<br>
<br>
##### **UPDATE**

At the beginning, updating records in mongodb could by a little bit confusing.
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
To do so, we need to use **$set** operator, as second parameter.

```js
> db.tools.update({ name: "mongo" }, { $set: { type: "nosql solution" } })
```
<br>
This operation updates first matching document, in order to perform that change
for all documents, we need to set **multi** operation to **true**.

```js
> db.tools.update({ name: "mongo", { $set: { type: "nosql db" } }, { multi: true })
```
<br>
**$set** is one of many update operators for [fields][update_field] and [arrays][update_array].

<table>
  <thead>
    <tr>
      <th> field operators </th>
      <th> array operators </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>$set</td>
      <td>$push</td>
    </tr>
    <tr>
      <td>$unset</td>
      <td>$pop</td>
    </tr>
    <tr>
      <td>$setOnInsert</td>
      <td>$pull</td>
    </tr>
    <tr>
      <td>$inc</td>
      <td>$pullAll</td>
    </tr>
    <tr>
      <td>$mul // multiply</td>
      <td>$addToSet</td>
    </tr>
    <tr>
      <td>$rename</td>
      <td>.$ // first match in array</td>
    </tr>
    <tr>
      <td>$min</td>
      <td></td>
    </tr>
    <tr>
      <td>$max</td>
      <td></td>
    </tr>
    <tr>
      <td>$currentDate</td>
      <td></td>
    </tr>
  </tbody>
</table>
<br>
##### **DELETE**

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

[cursors]: http://docs.mongodb.org/manual/core/cursors/
[update_field]: http://docs.mongodb.org/manual/reference/operator/update-field/
[update_array]: http://docs.mongodb.org/manual/reference/operator/update-array/
[query-operators]: http://docs.mongodb.org/manual/reference/operator/query/
