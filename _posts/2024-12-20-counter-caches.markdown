---
layout: post
title:  "Counter Caches"
date:   2024-11-20 21:30:00 +0000
categories: jekyll update
---

What are counter caches and how are they useful? Well the general idea is to provide an optimisation to an application which gives you information about the number of records associated to another record, from a count.

In normal SQL you can retrieve a count by:

```
SELECT COUNT(*)
FROM Products;
```

You could look at this and see that it probably wouldn't add too much load to the database, problems arise when you want to combine this query with very complex where statements with joins and includes. These types of queries can be extremely slow. For example if you had to add a new feature to provide statistics about your data, or in CRM software a feature to give the user information about the amounts of data avaialble. You could for example have a client_leads stat, which was calculated in terms of leads for the client who have contacts with certain sales reps and partners, which was accessible in a report.

To get this information from a database there are a number of strategies; the simplest of which would be to run a report every time the user accessed it. You could calculate these types of statistics in reports in advance, or you could cache the results for a certain amount of time. You also need to think about the requirement for accuracy.

One mechanism that Rails gives you is the in-built counter-cache. You need to directly add extra rows to your tables which store the current count of the associated records. 

You would start with a migration like:

```
change_table :posts do |t|
    t.integer :comments_count, default: 0
end
```

then you can add:
```
   belongs_to :post, counter_cache: true
```

Rails will increment the counter cache when a new child record is created, and will also decrement the count when a record is destroyed. In this way, the count is cached and does not need to be recalculated each time your running complex SQL queries, which can take a very long time for very large data sets. 

One of the main reasons for this article is to give an example of this simple optimisation which can reduce the load on your database but there is one caveat, and that is that the counter caches are not always accurate. This is important because even if you didn't have a requirement for completely accuracy, the counter caches can become out of sync. In this way the counter caches can drift and give you incorrect indications for data which are not a valid representation of your data.

In Rails one of the main reasons your counter caches can become out of sync is because if you have a complex application which is currently creating, deleting and updating records. Some times the counter cache will not update correctly. This could be for many reasons such as a dependent: :destroy not updating the counter or nested records being created by a parent. There may be some ways around this, but because counter caches are constantly running extra update queries once you've added them, it may not necessarily be a good idea to add extra methods to the default Rails counter cache behaviour. One way around this, which seems to be generally agreed upon, is to, at certain intervals reset your counter caches by providing reset_countercaches method. This in itself can slow down your database, so to fix this you can create a rake task which runs reset_countercaches, this can be triggered by a cron job which runs when your application is not busy. 

So overall is it worth it to use the counter_cache optimisation. I would say in most cases yes, because you will be adding a large improvement to your end users and reducing load on your database. Rails counter caches are a simple way to optimise complex queries. There are however lots of other ways to cache data, so considering the behaviour of your users is important. If the user tries to access a report and then constantly refreshes it but only accesses it once every couple of weeks, it may be easier to use some of the other Ruby on Rails caching mechanisms.