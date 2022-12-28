---
layout: post
title:  "The Other Way To Write Class Methods"
date:   2022-12-28 18:05:00 +0000
categories: jekyll update
---

There is another way to declare class methods, which isn't as obvious if you come across it somewhere in the wild. Normally you declare a class method like:

{% highlight ruby %}
class Rotor
  def self.signalify
    
  end
end
{% endhighlight %}

and obviously you use it like Rotor.signalify

However the other way is to open the class and declare your methods there e.g.

{% highlight ruby %}
class Rotor
  class << self
    def signalify

    end
  end
end

{% endhighlight %}

Then you can use it in exactly the same way, it's probably a preference thing but it can make it slightly easier to read if you have a lot of class methods.

It can also help you understand a bit more about how Ruby works, it turns out that each class has an Eigenclass, which stores instance level information. Ruby uses Eigenclasses to implement class methods as each of these class methods are defined on the Eigenclass.

A 'class method' in any language is a method in which a class is the receiver - that is the method is directly invoked on the class itself.

More information in this post:

[Eigenclass Class Methods](https://stackoverflow.com/questions/2025569/difference-between-self-method-name-and-class-self-in-ruby)