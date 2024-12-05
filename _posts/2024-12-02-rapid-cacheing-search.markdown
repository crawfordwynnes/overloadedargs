---
layout: post
title:  "Rapid Cacheing Search"
date:   2024-12-02 18:30:00 +0000
categories: jekyll update
---

This post is a very specific combination of two Ruby mechanisms, it will give you more of an introduction into the `<=>` operator.

## Search

The problem domain for this solution to search is actually quite narrow, if you think about most Web Applications it's very likely that the problem you are trying to fix does involve a database.
We do know, however that, for example, log files usually come in the format of text files, so problems involving logs could be a potential for optimisations with the following techniques. 
It's also worth considering Redis for simplifying search problems as it will give you access to lists with a larger memory footprint.

One of the easiest go to techniques for solving search problems in Web Applications is Elastisearch.
Elastisearch allows you define types of data that you want to search over, and then gives you easy to use rake tasks for indexing that data.

So if you wanted to search by name for something and you added it to your database, then you would need to reindex your data after that data was added to your database. Reindexing is usually quite an expensive operation so it's something you *can* do when your application is not busy. 

Anyway the problem that this post refers to is one, where similarlily to elastisearch, you do need to preprocess your data. It differs in the way that when the data is updated there should be an easy solution for non tech people to update the data.

One of the easiest way of allowing this is by using a text file, so someone can go in and add or remove data from it, or upload a file of some description.

Using a text file allows a user to completely replace the entire file on a whim, so imagine an entire data file that can fit in Ruby memory.

The file also needs preprocessing in terms of sorting the data. The preprocessing in this way also validates that the data is sorted; because every time it is copied into the system it is sorted, 
Although, this is more expensive than keeping the text file sorted.

An optimal solution is to attempt to keep the text file as sorted as possible, to minimize the ammount of time it requires to sort the file, which would need to be communicated to stakeholders. A rake task and documentation can always help.

## Spaceship Operator

What is the spaceship operator `<=>`, and how can it be used for search? It's best described with an example:

The spaceship operator used on it's own can return one of three values, depending on the result of the comparison, so it can be -1, 0 or 1; if it's less than, equal or greater than. It applies to integers, strings and any object where the <=> is implemented. 

```
For Integers:
1 <=> 1  #=> 0
0 <=> 1  #=> -1
1 <=> 0  #=> 1
```

```
For Strings:
"abc" <=> "abc" #=> 0    (equal)
"ab"  <=> "abc" #=> -1   (length of string)
"abb" <=> "abc" #=> -1   (string is less than)
"abc" <=> "abb" #=> 1    (string is greater than)
"ABC" <=> "abc" #=> -1   (capitals makes it less than)
```

The `<=>` is the three way comparison operator, and we can override any of the operators in Ruby, for example, if we want to use custom behaviour where a string of capitals is less than a lowercase string. However for this
example we don't actually need to provide custom behaviour, because the important thing is that the
`<=>` gives us an ordering, it's not actually necessary to have a fully correct ordering to create a O(Log(n)) search, the important thing is that the ordering is there.

This is slightly different to method overloading, where you specify many of the same name functions with different parameters, which is used in C++. Method overriding simply invovles overriding a method which is specified in a subclass.

The idea here is to give an ordering to strings, so that when they are sorted we can use an O(Log(n)) lookup on the list to find out if the key exists. Normally we can use the .include? method in Ruby, but that would iterate over the entire list and be O(n). The point of the O(Log(n)) search is important for large lists, and takes advantage of entropy from Claude Shannon. To improve the speed of the lookups we take advantage of the structure of the data instead of increasing space complexity.

Luckily in Ruby we have a method which can take advantage of our sorted data, it's called `bsearch` and the way it works internally, using binary search it checks the middle key and determines the outcome of the comparison, if it's -1, go to the left midway point and if it's 1 go to the right midway point. 

It would look like this, from the docs:

```
a = [0, 4, 7, 10, 12].sort
a.bsearch { |x| x <=> 4 } # => 4

a = ["aa", "ab", "ac", "acc", "attt"].sort
a.bsearch {|x| p x; x <=> "attt"}
```

## Class Cacheing

The second specific Ruby mechanism that we can use here is class cacheing. This takes advantage of something 
that we have seen with memoization. 

This comes in the form of:

```
@instance_var ||= computed_value
```

Except in this case we are going to use class variables:

```
@@computed_list ||= sorted_list
```

Using class variables with ||= means that any class that uses this method will return the cached version of
the list. This means that it's not just the instance of the class that can cache the memoized value. This is
important for systems that share the sorted_list across people that are accessing the server using that Ruby process, which can greatly improve speed for applicaitons with many users.

## What Slows Down Data Lookups

One of the main problems with using this kind of pattern is that it relies on the data actually being able
to fit in a Ruby Object, beyond that it would make a lot more sense to allow the database to do the heavy lifting because that's what the database engine it is designed to do.

It's kind of like when you consider your optimisation and pull a certain amount of data out of the database and then use Ruby to iterate on the result instead of having to write very complex SQL, even if your putting that SQL into the codebase without ActiveRecord. It's a balance, but hopefully if you read to the end of this you will see that the `<=>` operator can be very useful for certain applications.