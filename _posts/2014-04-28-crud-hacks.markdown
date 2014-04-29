---
layout: post
title:  "CRUD hacks"
date:   2014-04-28 16:06:19
categories: mongodb
image: http://www.fillmurray.com/300/200
image_src: http://www.fillmurray.com
---

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