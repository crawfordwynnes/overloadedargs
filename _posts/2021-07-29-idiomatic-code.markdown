---
layout: post
title:  "Idiomatic Code"
date:   2022-08-15 18:08:08 +0100
categories: jekyll update
---
{% highlight ruby %}
||= []
{% endhighlight %}

Sometimes whilst writing Ruby code we have to use a pattern that is repeated a lot when building arrays. Idiomatic code to achieve this looks like this:

{% highlight ruby %}
def build_array
  @array = []

  (1..array_length).each do
    @array << rand(100)
  end

  @array
end
{% endhighlight %}


This can be changed to:

{% highlight ruby %}
attr_reader :array
def build_array
  (1..array_length).each { @array ||=[]; @array << rand(100) }
end
{% endhighlight %}