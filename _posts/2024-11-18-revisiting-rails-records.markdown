---
layout: post
title:  "Revisiting Rails Records"
date:   2024-11-18 14:15:00 +0000
categories: jekyll update
---

# .sole

If this post is a TLDR; and you don't know about the .sole method, all you need to know is that with the `.sole` method added in Rails 7, you have a really nice way to build your Active Record queries in a more defensive way. The `.sole` method will return an `ActiveRecord::SoleRecordExceeded`, if you've run a query and you get more than one record returned. This can be useful for continually ensuring that your primary keys are all unique or for doing a kind of continuous data validation on any other types of enforced unique data, e.g. in a Rails migration adding `unique: true`.

# .load_async

There isn't much of a follow on from the above method except to say that load_async is a new way for Rails to optimise your ActiveRecord queries. Adding `load_async` to to your method calls will allow your controllers and models to push the loading of AR objects into the background and continue with their current thread. If you use load_async then Ruby will create a thread which will go off and grab a database connection from the connection pool, usually specified in the database.yml. This should give you a large performance increase. 

# Objectionable opinion on load_async

If your database is running on it's own machine and it receives many requests in a row, your giving your machine a request to do multiple things at once, which essentially relies on having native Ruby threads which can communicate with the native CPU cores on that machine, even with only concurrent execution your potentially offloading a decision on database concurrency further down the line. To me this is confusing when you try to get to grips with how ActiveRecord works. For example: let's say you end your initial statement with an ActiveRecord .where. At this point your still building the query so that AR has the ability to chain multiple statements together. It's not until you finally run an .to_a, .all, .first or a .sole that the AR Singleton interface that youv'e grabbed will be converted into an actual query. Based on this a .load_async would be one of these types of end statements which actually sends the code off to run the query, this is quite complicated when you also consider the difference in behaviour between .includes (not immediately doing an AR query) and .joins (immediately doing an AR query).

# Lazy/eager loading

Following on with a bit less of a tenuous link than the different between .sole and .load_async, and building queries with .includes and .joins, where .joins is a form of lazy laoding and .includes is a form of eager loading, we also have the difference that .includes *could* load records with seperate queries and joins works with just the one query. 

From the Rails docs a Psql IN statement from an includes:

```
User.includes(:address, :friends).to_a
 # SELECT "users".* FROM "users"
 # SELECT "addresses".* FROM "addresses" WHERE "addresses"."id" IN (1,2,3,4,5)
 # SELECT "friends".* FROM "friends" WHERE "friends"."user_id" IN (1,2,3,4,5)
```

# N+1 Queries

The N+1 problem is a well known problem within Database lookups, the problem is that if you build a query that returns N records, then each of these N records goes off and does an additional M lookups, you can very quickly be in a place where you've created a large amount of uneeded queries which can badly slow your database down. You can create an N+1 problem quite easily by running a query that returns a number of objects, for example with `.all` or `.to_a`. then assigning this to a variable and then looping over that object and running additional queries. The reason this is such a problem is because it does not scale well. If your initial query returned a hundred records, then instead of loading the association in advance on these records you can get an additional number of queries per result. So if the second set of queries ran 100 times, then for your 100 results you would get 1 * 100 overall queries instead of 1 * 2. This is also the case if you do additional queries on the second set of records, where you would end up with the case of 100 * 100 additional queries. If you imagine this is per request then you can see it would not scale well in a system with multiple users.

Fortunately in Rails we also have a number of other methods than just using includes to solve this, we also have .preload (multiple queries) and .eager_load (LEFT OUTER JOIN single query), using *includes* will choose which to use; .preload or .eager_load, so using `.preload` or `.eager_load` on their own is making a decision in advance about the best way to optimise that query.

There are tools for scanning and attempting to find N+1 query problems, if your looking for a bit more of a broad approach to optimising.

# Shallow clone, deep dup

What can happen when you've run your AR queries and want to do something with your data? Sometimes your going to get a a very complex Object in Ruby with multiple levels of nesting, for example in a complex hash with multiple levels of arrays or hash within it. In this case sometimes we want to create a new object from the original data to work on, to do this we can use methods like ```@object_1 = @object_2.dup```. In this scenario we will get a new object with a new object id, and to the uninitiated to the world of Ruby pitfalls, if we attempt to modify this Object we can get results that were not intended.

This is especially true if we try and run destructive method calls (usually referenced by a !). Let's say you got a complex object and were looping over it, attempting to figure out what parts of the object had changes, if you modified the deep nesting of the copied object it would also modify the deep nesting of the original object and you would not see any changes. To get around this problem, in Ruby we can use the `.deep_dup` method, which will create a new Object with new references for deeply nested parts of the object. Problems like this in Ruby can really give you problems when ensuring the integrity of your data, so it's best to cover these parts of your code with tests.

# Difference between &. and .try

If everything is going well, chances are you've got an object which is suitable for sending through to the view layer. Originally in the Rails world, we had the Rails specific method try, which you could invoke by using a symbol of the method name, like 

```<%= @object_one.try(:visit).place %>```.

 The try method in this example is there in case the visit method decides to return nil. It's like saying, please display this attribute to the user if you can, but if visit is nil, catch the exception raised by calling place on it. A bit later Ruby also got this behaviour in the form of the &. operator which does nearly the same as the try method except it will raise a NoMethodError if the subsequent method, in this case place is not implemented.

# Fetch/dig

`fetch` can be used to access a key and combined with another argument, if the key is nil then it can return a default value which is very useful.

You can use `dig` with a number of keys to go into an object a number of levels, for example a nested hash containing the keys (:attr_one, :attr_two, :attr_three) being called by dig with the attribute names will access the value in attr_three.

# Hashwithindifferent

Hashwithindifferent access is an easy way of converting an object so it has access via keys of strings or symbols. It can be used in many places and is part of ActiveSupport in Rails, for example from the docs, you can use:

```rgb = { black: '#000000', white: '#FFFFFF' }.with_indifferent_access```

and access rgb with a string or a symbol.

# Reflection

Reflection is the ability to inspect code in the system or itself. You can use the Model.reflections method to get all of the associations on an ActiveRecord object in Rails, for example taking a string and then introspecting the associations and matching the correct object.