---
layout: post
title:  "Proc and Lambda in Ruby"
date:   2022-08-30 10:45:00 +0000
categories: jekyll update
---

This post is more of a historical post about a feature in Ruby that has some nuances that were talked about a long time ago. They aren't really that important unless you are writing code which is doing a deep dive into specific behaviours of Ruby Proc and Lambda.

Before we start let's remind ourselves what are Proc and Lambda and what can we do with them.

First of all let's remind ourselves of what a Block in Ruby is, it's defined as any code within the do and end block or within curly braces { }. Usually do and end is styled as a multi line block and the parentheses are styled as code which fits on one line. For example
most of the enumerable methods like .each are able to receive a block as an argument which is
why you can write Ruby in a functional style more easily e.g. [2,3,4].each { |x| p x + 1 }

Some people use blocks quite a lot as you can call yield to execute the block or implicitly use the .call method on the block to run the code. This becomes much better when you can change the behaviour of the method by using .block_given?

Lambda's are a way of using specific syntax to save a block into a variable for passing around:

{% highlight ruby %}
block_code = lambda { puts "This is a lambda" }

or 

my_lambda_with_args = -> (v) { puts "hello " + v }
{% endhighlight %}

In the above literal operator syntax that everyone knows with -> that looks suspicously like es6 JavaScript functions that everyone knows are called 'stabby lambda' syntax. You can see them used quite a lot in Active Record scopes for readability.

Lambda's are known as anonymous functions because they have no name.

A lambda happens to be a special type of Proc. A Proc is used for the same reason to pass a 
block of code around and then run it. The point of this article however is to demonstrate and remember that there are subtle differences between a Proc and a Lambda, and these go further than just the way you instantiate them (= -> and Proc.new) and if they respond to the .lambda? method. 

These differences include the context from which each will return, e.g. if you write return within the Proc it will return from the method that it's within whilst a Lambda will only return from the Lambda itself. This is important for debugging control flow once you've decided that you want to start passing blocks of code around, also a Lambda will raise an exception if you pass too many arguments. Finally there is also a loose concept of closure where the execution context is bound to where it was created. For example if you create a variable in one class in a closure and the pass the block and modify the value, the original value will have been modified.