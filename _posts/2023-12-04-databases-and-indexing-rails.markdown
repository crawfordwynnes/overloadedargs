---
layout: post
title:  "Databases and Indexing in Rails"
date:   2023-12-04 16:30:00 +0000
categories: jekyll update
---

This post is all about databases and your persistence layer, we have to mention the majestic monolith, because if you seperate your apps you will have to manage your migrations in one of the applications. However if you do decide to manage all your migrations from a central application you can just mention it in a Readme and it will become a process that you have to manage. Rails engines are a way split up a code base and still keep it as part of the same application which can make managing migrations a bit easier. Migrations should be able to run all the way up and down without stopping.

# ORM

To get your Rails model layer correctly working with the database you need an Object Relational Mapper (ORM) called ActiveRecord, which translates types of lookup calls into for example SQL statements which get sent through the database adapter (usually a Gem) to the database. For example a really long time ago mysql was the most popular choice for databases, to use it you would specify the mysql gem in your Gemfile, this was updated to the mysql2 gem which was a much improved version.

Database scaling is usually described as scaling vertically whilst application code is described as scaling horizontally. In practise this basically means that a database server (which can be a different machine to the application code), if it starts failing because of requests and latency then additional memory and connections are supplied so that all of the requests can be served. The amount of max_connections can be specified in the database.yml, and additional tools can be used to monitor the database, e.g. NewRelic. One of the best tools to get insights into your database is PgHero. It will recommend indexes, give you an interface into explaining queries and highlight areas where there may be N+1 problems.

# Indexing

Where do we put all of our indexes, even PgHero/Bullet recommendations may not give the best configuration for you. In general indexes, which allow faster database lookups, should be placed on foreign keys and data that is frequently searched upon. This should be managed in the Rails migrations. Other things that can be indexed include data that is frequently searched upon with an order. A newer type of index is the concurrent index, which is described as 'does not block concurrent writes'. What is happening here is that normally when data is added to a table via INSERT, UPDATE or DELETE then new indexes would need to be generated, which is why there is additional load for indexing and why you shouldn't add indexes to everything. Also this implies that indexes are more useful for applications which have high reads. If you add a concurrent index in Rails with `algorithm: :concurrently` and when there will not be a lock on the database when the next update comes in; multiple indexes can be calculated at the same time. To create these indexes you need to also use `disable_ddl_transaction!` because of how transactions in migrations work. This type of indexing is specific only to PostgreSQL databases, even though indexing is not only for SQL type databases, for example, MongoDB can also use indexes, although these are not managed in migrations.

Let's say you've added your indexes, and the tools you have at your disposal no longer suggest additional indexes, how do you convince yourself, other than load times that your indexes are working correctly. As it turns out, it's actually quite difficult to convince yourself that your indexes are working correctly. The basic methodology is to run an EXPLAIN query on the fields your looking up on, in Rails you can use the *.explain* method. 

For example running `User.where(email: "test@example.com").explain`, should result in one of two different responses depending on if the index is set up correctly. If the index is working then you should get a response which incldues either 'Seq Scan on users' or 'Index scan on users'. If the index is working then you would get an Index scan, however, currently Psql databases are configured to run sequence scans even if your SELECT returns more than 10% of the size of the records in that table. This means that to really convince yourself that an index scan is happening you may have to add a few records on empty tables just to be sure that your SELECT is triggering an index scan.

# Sharding

Rails 6 and 7 have added advanced database sharding techniques. Rails 6 added the switching connections per database, which provided the groundwork for what we can now use as a database.yml with an additional yml key under each environment with a different database name, each database can additionally be marked as *replica: true* which gives very easy access to database redundancy.

There have been some interesting concepts implemented with multi databases, some of which includes doing JOINS across multiple databases, which I've never seen in practise but you can read about it being used at [Github](https://github.blog/2021-07-12-adding-support-cross-cluster-associations-rails-7/). I think the general principle here about eliminating JOIN queries is based on the method of optimising a query at code level by building two lists and then doing lookups on the data in memory.

Potential multi-database problems can be improved with database selector middleware, where an additional delay can be added, e.g. adding config.active_record.database_selector = { delay: 2.seconds }. This kind of configuration could improve read replicas when records are written to on the primary db.

# Optimisations

There are many more database optimisations that can be used. Some of these are practical and some are design considerations. The design considerations have some foundations in database theory. An example of a practical optimisation that can be used in PostgreSQL is vacuuming, which is the equivalent of a garbage collector for a database, I believe that it runs automatically. One of the more important optimsations are the design considerations, and these include the actual layout of the tables, and the data types and keys. Before getting in depth with theory it can be important to look at the overall technology design choices, as the database should represent the shape of the data, so for example if your data should be modelled in the shape of a graph then a graphDB should be used. Mongodb, for example represents a database that represents documents, you can add transactions to mongodb if you need. MongoDB is described as a NoSQL solution as opposed to a RDBMS, mainly because when you interact with it on the shell, you run queries on database objects like db.posts.find({likes: {$ne: 1}}), (the $ne matcher means anything that does not equal). There are also key/value store databases such as Redis or Google Firebase which can also be used as a cache.

# Theory

Database theory includes cap theorem, which is important to people who build databases. For example the Consistency, Availability and Partition Tolerance in SQL databases is more in line with the data being consistent.

Databases also include ACID compliance, which extends from the database level to the code level. This type of theory relates to building database API designs and internals so that data correctness can be guarenteed in different locks, called transactions. Different types of databases can conform to different parts of Atomic, Consistent, Isolated and Durable. Adittionally transactions can be wrapped in transactions, this can be controlled at some level by ActiveRecord transactions in the code, I have never done this but I believe experimenting with this would, for example, involve trying to send multiple writes at the same time and cancelling one, type behaviours.

There is also normal form, which provide rules to provide the correctness of the design. This consists of different levels of normal form (1NF, 2NF & 3NF), which provide rules to provide the correctness of the design, and reduce problems with anomalies using logic.

For example proceeding through optimising normal form would optimise replicated user names with multiple telephone number fields, so that you don't need to UPDATE multiple rows to update one piece of data. Individual phone numbers would be seperated out into records which would be 1NF, then a new table would be added with a foreign key on user id in phone numbers, this means that there would be no partial dependencies, or 2NF. Lastly there would be no transitive functional dependencies for 3NF, so for example you have a table with a primary key and another piece of imformation like an id and a piece of data and the data is determined by the primary key via the additional id, then this should be seperated out into a JOIN table. The process of proceeding through doing these changes into a working database is called normalization. The most interesting thing about normal form is that database designers rarely think about it, they just have done it before so their designs for patterns are intuitive. There is also database denormalization which is the process of placing tables of data into the same table to increase performance. For example a user and an address table but every user only has one address so there will be no update anomalies.

# Backup

So we've got our lookups working, and it's all correct and super optimised, what happens if there is a power outage, downtime or some other unknown event, how are we being responsible with peoples data and conforming to GDPR. Most of the time we need to create a procedure to store backups. These can be simple or complex depending on where your data is. A general pattern is to encrypt your data backup before transit, this is called encryption in transit and in-situ or at rest. If your using a service like AWS then they provide an easy way to take database backups and to store them. These can then be deleted after a certain amount of time in a rotation. Most importantly when checking that your backup system is working is to make sure that the procedure for backup restoration is also working. Simple ways to start looking at backup procedures include `pg_dump` and then accessing the data via scp, this can be useful for building data for staging systems. For mongo there is `mongodump` and `mongorestore`, if your using a system like Heroku you can write down a plan for logging in and backing up via console. Building backup procedures can be similar to when building out the deployment, a lot of it involves keeping a record of the actual commands used. Another thing that may be considered for backups and security, is that for systems where there are load balancers and databases are on different servers, then there could be a check to see if for example the TLS connection is going through the load balancer to the database server.

# Code Techniques

An older technique for optimising database calls was to look at ActiveRecord queries that were taking a long time and to try and optimise them by writing them as pure SQL and then executing that code directly. There was also Arel and Squeel, which improved on ActiveRecord, Arel became part of ActiveRecord.

Another technique which can be used in databases is the idea of the soft delete. The soft delete looks like it has removed the requested data to delete from the user, but the data is simply marked as deleted. This kind of pattern can be useful for ensuring a papertrail of data. It's also useful if the data at some point was intended to be restored. In Rails you can achieve soft deletes with:

```
included do
  default_scope { where(is_deleted: false) }
end
```

To incorparate soft deletes properly you can include a concern with an overwritten destroy method which runs the actual destroy callback which you have originally overwritten. This would be so that when an administrator was working on the console and they ran for example User.destroy, then the soft delete method was called instead. The full delete method would instead be User.delete.

In Rails 5 a change was made where it was made the default for foreign keys to exist whilst creating related objects, before that `required: true` was needed on relationships. I believe that you can in Rails 5 onwards explicitly set require: false, if you want a check for referential integrity to be turned off. A full check for referential integrity involves checking the related object also exits. There is also potential to create records in relatinships whilst specifying the deferrable
[Deferrable](https://blog.saeloun.com/2022/03/30/rails-7-postgres-fk-deferred.html) keyword.
In Rails as your building out your application you can also, afterwards go through and specify `dependent: :destroy` on relationships which can much improve your workflow.

For pagination you can now easily use tap and scopes to create paginated data with Ruby:
[Complex Active Record Queries with tap](https://jes.al/2015/08/building-complex-activerecord-queries-with-tap/).

# Other Topics

Other advanced topics related to building applications with databases include:
 * Removing join tables by changing the structure of the database.
 * Removing has_and_belongs_to_many in favor of has many through.
 * Composite keys, e.g. `self.primary_key = [:user_id, :id]`.
 * Understanding of Join order, using WHERE on a JOIN and when it's not possible to create a JOIN.
 * Understanding CROSS JOIN and when to use it.
 * Advanced sub query design and usage including the IN query in psql.
 * Using tools like mongosh for debugging.