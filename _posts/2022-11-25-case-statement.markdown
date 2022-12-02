---
layout: post
title:  "Ruby 2.7 Case Statements & Pattern Matching"
date:   2022-08-30 10:45:00 +0000
categories: jekyll update
---

The Ruby case statement has been around forever. It's been used and misused in many situations where a piece of code can be reduced from a long if statement with multiple else clauses. In my opinion, 99% of the time if your matching over three or four case statements it's a sign that you should probably try and refactor your code and create small Objects. Sometimes this can involve a bit of extra effort to get right but it will be worth it. A way that may help you to look at it a bit better is to do case statements with Objects then move to a method which generates the Object name. You can do this with .classify.constantize but a better way to do it is to store the class names in a constant or class to avoid metaprogramming methods. Once this is set up you will be able to call shared methods on those objects that may be provided with inheritance.

With that all aside in the not too distant past Ruby got pattern matching, a functional language feature released in Ruby 2.7 and then improved with Ruby 3.1. This was quite an interesting extra way to use case statements with the 'in' keyword. The basic example is:

{% highlight ruby %}
hash = { place:{ description: "new", size: 637,  names: ["type1", "type2"]} }

case data_structure
  in { place: {description: "hello", size: 637}}
end
{% endhighlight %}

You will notice that it matches at the in-expression. If you wrote this as a case when it would only evaluate when there was equality instead of matching patterns.

You can match on types for example and these will evaluate to true. New things that were added to pattern matching in 3.1 include using the pipe symbol | to match multiple expressions, assigning matches to a variable with => x and ignoring values in a pattern like

{% highlight ruby %}
  case [1, 2, 3]
    in [_, a, 3]
  end

  a => 2
{% endhighlight %}

There is also the pin operator which looks similar to the logical operator for not but it's checking for the same expression. For example when the array has a different value and you check the pattern [a, ^a] you get false but if it had the same value in the array [1, 1] then it will match. To fully illustrate why this is the case try and use a pattern containing the same value [a, a]. It will give you a duplicated variable name (SyntaxError).

{% highlight ruby %}
  case [1,2]
    in [a,^a]
      puts "h1"
    in [a,b]
      puts "h2"
   end

=> h2

  case [1,1]
    in [a,^a]
      puts "h1"
    in [a,b]
      puts "h2"
    end

=> h1
{% endhighlight %}

You can also use the splat operator for variable array length in the pattern. The thing that's not immediately clear when using 'case in' is that it can be combined with an if statement. If you've tried to convert a 'case, when' to a 'case, in' then you may have to add the extra conditionals which is really not what you want to do. To get your head around it a bit more try to remember that a 'case, in' is for matching expressions not variables.

Here is Yukihiro Matsumoto announcing pattern matching at RubyConf 2019.
https://www.youtube.com/watch?v=2g9R7PUCEXo
