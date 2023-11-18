---
layout: post
title:  "Ruby Method Argument Types"
date:   2019-10-23 11:08:08 +0100
categories: jekyll update
---
There are several features of Ruby which take a bit of getting used to as the syntax for them isn't immediately clear what they do. Some of these are explained here including what they do and how to use them.

### Default Args

Ruby takes arguments as default when declared in parameters, so when the method is invoked if the parameter is not passed in then it is assigned to a variable.

e.g.
{% highlight ruby %}
def foo(x = 3)
   x
end
{% endhighlight %}

if you call foo without an argument it will return 3.

### Optional args

Arguments can be optional if you use default arguments with nil as a value in the parameter.

e.g.

{% highlight ruby %}
def foo(y, x = nil)
   p y
   p x if x
end
{% endhighlight %}

There is an order where normal arguments are defined first.

### Star args

Ruby can take a list of args and expand them to an array for you if you declare the parameter with a *. This should not be confused with pointers in C.

e.g.

{% highlight ruby %}
def foo(*x)
   p x
end
{% endhighlight %}

foo 1, 2, 3 will print [1, 2, 3]

### Double star args

You can give a list of key values and have them combined into a hash with ** before the parameter name.

e.g.

{% highlight ruby %}
def foo(**bar)
   p bar
end
{% endhighlight %}

{% highlight ruby %}
foo(hello: world, hello: day)

returns:
{ hello: world, hello: day }
{% endhighlight %}

This can be combined with other arguments e.g.

{% highlight ruby %}
def (x, **bar)
  p x
  p bar
end
{% endhighlight %}

{% highlight ruby %}
foo (3, hello: world)

returns:
3
{ hello: world }
{% endhighlight %}

### Keyword arguments

Keyword arguments are a new feature for Ruby 2.0 and allow you to set default arguments in the definition and can be called with a keyword

{% highlight ruby %}
def foo(x: 1, y: 2)
  puts x
  puts y
end
{% endhighlight %}

the above method can be called with foo(x: 1) 

If you called the method with foo(x) you will get undefined local variable or method x

As a note in the past were hash arguments which were very popular and are no longer needed as we have keyword arguments. To get hash arguments we assign a hash as the default argument.

e.g. 

{% highlight ruby %}
def foo(x = {})
  p x
end
{% endhighlight %}

{% highlight ruby %}
foo (x: "hello")

will return
{:x=>"hello"}
{% endhighlight %}

This pattern is sometimes seen in Ruby elsewhere where you can assign an empty array in a block to make code more terse.

### Conditional Assigment

When being flexible with arguments you can also use conditional assignment if the variable is nil.

e.g.

{% highlight ruby %}
def foo(bar = nil)
   bar ||= "value"
end
{% endhighlight %}

which will allow you to have an optional default argument where you overwrite nil if it's passed in.

Calling foo(nil) will result in "value".

If you used default arguments and passed in nil you would get nil so the behaviour is slightly different. It can be useful for when you use ARGV to grab input.

As a side note {% highlight ruby %} ||= {% endhighlight %} is sometimes used for caching values instead of control flow which apparently could be more akin to Perl.

### Boolean Methods

Boolean methods are really simple and are more of a convention. If you intend your method to return true or false then you should add a ? at the end of your method.

e.g.

{% highlight ruby %}
def foo?
   true
end
{% endhighlight %}

There are extra rules for Objects that are Truthy of Falsy when doing things like calling nil? on nil. In different programming languages values can be truthy or falsy. A way to think about it is if values like nil or 0 satisfy the expression of equality with true then they are truthy. This get's more confusing when you think about multiple expressions of equality. For example in JavaScript, built into the language you can use == and === to try and compare objects and values the that object provides. A way to think about this is with geometry. A square is equal to a square but not equal to a square with a different area.

### Destructive Methods

Destructive methods are also a convention and signify that if the method modifies the underling data then it should be denoted with an explanation mark ! at the end of the method.

e.g.

{% highlight ruby %}
x = [1, 2]

def foo!(y)
   y << 3
end

x
=> [1,2,3]
{% endhighlight %}

In this way we could say that x has been mutated. If we called .freeze on x before hand we could prevent this and it would become immutable as it would raise an exception. You can see this in Javascript and Rust with the let operator. These methods are also called dangerous methods and supposedly they come from the Scheme language.

All Ruby's methods from the Enumerable class are non-destructive by design. The Ruby pop method doesn't have an ! but it's still destructive.

https://launchschool.com/blog/mutating-and-non-mutating-methods

### Method Missing

Method missing is more of a feature of Ruby and something you can use as a pattern to help you. If you are starting off in Ruby you should try and learn this early on, however getting it rite can be hard because of some other tacets that you need to take into consideration.

If you call a method that doesn't exist on a class you get a method_missing error. (Ruby uses duck typing here to look up the method regardless of it's type, if it's quacks like a duck..) As a programmer you can use this to your advantage and define a method called method_missing. You can use all sorts of tricks and metaprogramming tactics to then call other methods based on the type of values passed in.

The gotcha's here are that you should override respond_to_missing?

In Ruby there is also the opportunity to use metaprogramming techniques with const_missing.
This was suprising to me because because it's a Ruby implementation of using a method that's called after catching an exception.

### Explicit Return

Explicit return is something pretty simple, it just means that you can use the return keyword instead of letting Ruby return the last value in the buffer of the method. e.g. this is explicit return.

{% highlight ruby %}
def world
  return "hello"
end
{% endhighlight %}

vs

### Implicit Return:

{% highlight ruby %}
def world
  "hello"
end
{% endhighlight %}

### Explicitly Passing Blocks

Blocks can be passed to methods by using the & character and we can use call on the block to execute the block.

e.g.

{% highlight ruby %}
def foo(&block)
  call block
end
{% endhighlight %}