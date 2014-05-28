---
layout: post
title:  "Mongodb Applied Design Patterns"
date:   2014-05-21 16:06:19
categories: mongodb
---

<div data-alert class="alert-box secondary">
  Watch out, it's still a draft of the article and may contain small bugs. But feel welcome to read:)
  <a href="#" class="close">&times;</a>
</div>

Here's a few of my thoughts about **"MongoDb Applied Design Pattern"** written by Rick Copeland.
<br>
Presented book consist from two parts and I will focus on first part of the book.
<br>

<a href="http://shop.oreilly.com/product/0636920027041.do">
  <img src="http://akamaicovers.oreilly.com/images/0636920027041/cat.gif" />
</a>

#### Design patterns


**Embed or reference**

The books begins with introduction of most common puzzle during designing document database schema.
MongoDB is called as "schema-less", but this don't mean that our data has no structure.
<br>
It just don't restrict how entries should look like for each input/update operation, but schema comes from
your application data model. The puzzle is to decide between *normalized* or *denormalized* structure of
data in database.
<br>
<br>
In rational database system (SQL), normalization is almost always preferred approach.
When we use them, we have simplified task, because choice is obvious. Using document data store, like MongoDB,
there's no silver bullet.
That's the reason why in this book, phrase "It depends." occurs so often.
Important lesson from this chapter, is to think about most common queries and it's results,
before we design our schema.
<br>
If client queries ask often about only embedded collections, without using parents data,
better solution is to move this embedded collection to separate collection.
<br>
Next reason for normalization is using only small fraction of embedded documents,
this leads to waste of time and memory.
<br>
<br>
Author also mentions about size limitation of single documents: 16Mb.
If there is risk of getting close to that limit,
don't hesitate to extract most populated embedded collections.


**Polymorphic schema**

Flexibility in structure and content of each single documents, save us from a lot of troubles,
both with migrating the date when schema changes and integrating database with client systems,
based on object oriented programming paradigm.
For example, when we had inheritance in our data models, we could store all instances of parent class,
in single collection. Schema flexibility allows us, to introduce new fields or change fields types, there is no need of data migration.

**Mimicking transactional behavior**

MongoDb do not provide transactions for multiple operations. So we are forced to rely on atomic operations.
At this point, we need to remember about first chapter, and use embedded documents for making all-in-one updates,
as substitute for transactional updates.

I suggest to read this three chapter as obligatory material, for those who are beginners in NoSQL world.

#### Use cases

Second part consists of six chapters, each one is dedicated to different domain problems,
starting from applications logs aggregation, through building Content Management Systems,
ending on planning online games platform.
I recommend to just look at each chapter, in order know where you should look for a advice,
when you will face similar puzzles in real life projects.
Don't be biased and don't drop chapters that are not in your main domain completely.
There is plenty of useful tips in this second part of the book, but you probably want
read all parts with details.
<br>
That content is quite similar to **["50 Tips for MongoDB Developers"](http://shop.oreilly.com/product/0636920019893.do)**.
Although, understanding concepts from first part "Design Pattern" is necessary for
understanding "Use cases" part and book mentioned above.
