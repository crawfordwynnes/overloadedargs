---
layout: post
title:  "Struct Inheritance"
date:   2022-12-18 11:45:00 +0000
categories: jekyll update
---

Here is a quick post to remember the days in Ruby that we used to use the 
struct inheritance pattern when we didn't want to define classes with loads
of attribute methods. As a quick shortcut to building a full object we can build
a class that just inherits the methods from a struct. This
is because everything in Ruby is an object. You can also define methods inside
your struct class if you use this pattern.

{% highlight ruby %}

class Stat < Struct.new(:time, :detail)
  def fullstat
    details.to_s + time.days.ago.to_s
  end
end

{% endhighlight %}

The normal way to use a struct is:

{% highlight ruby %}

Stat = Struct.new(:time, :detail)

{% endhighlight %}

You would use this struct class like this, which is exactly the same as a normal class:

{% highlight ruby %}

Stat.new(Time.now, :warn_log).fullstat

{% endhighlight %}

Your not supposed to inherit from this class and it's meant to be lightweight. If you wan't something with more flexibility then you can use OpenStruct by requiring 'ostruct' which will return an instantiated object.

Normal instantiated structs can be compared and also get enumerable methods on them.