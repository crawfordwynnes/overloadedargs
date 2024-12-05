---
layout: post
title:  "Ruby Cacheing"
date:   2024-11-22 17:30:00 +0000
categories: jekyll update
---

I'm going to use this post to build up slowly the idea of cacheing in Ruby, I've got it wrong in the past and not thought about it properly, so perhaps if you read to the end of this post you will have a better idea of what's actually going on when you see some code that uses the Or-Equals `||= operator`.

First of all, what does it mean to use the `||= operator`. Well it means please *assign a value to the left, the value on the right if the value on the left is nil.* That's basically it, there is no magic things going on here. The problem arrises when it is claimed that the `||= operator` can be used for caching, because automatically a Ruby Developer would think that if I use `||=` I will get caching, and caching in this way would refer to the fact, please assign a value to the left, the value on the right if the value has *changed*, but don't run the long running query (the part to be cached on the right) if we already have a value. So does using the `||= operator` make sense or not? To fully get to grips with this we have to take a step back and say, whilst this class is available, with params or not, is the result of the complex operation going to be the *same* every single time. 

If it is then we can successfully cache it using this method. A really basic example of this could be, please accurately calculate the perimter using these values and store it, so next time I need to calculate it, please return that value.

Part of the confusion can come from the fact that you can give different parameters for the area and store many calculations in advance, this is essentially memoization, and we call this part of it caching. To achieve memoization in Ruby code with different parameters you would write your code like:

```
class PerimeterCalculator
  def initialize
    @cache = {}
  end

  def perimeter(width, height)
    @cache[width][height] ||= calculate_perimeter(width, height)
  end
end
```

The first time you call perimeter, it will go off and calculate the result, and store it in the cache, the next time you call perimeter, Ruby checks to see if @cache[width][height] is not nil and returns that value instead of going ahead and calling calculate_perimeter.

So what does this actually mean in practise? Well what would happen if we tried to use this for loading the results of long running api requests? Well if the result of that api request is not going to change for the lifetime of your object, then you could successfully use it to cache the request, this would also apply to for example very long running ActiveRecord query, if they returned the same result. But if we think about it a bit more, it could be very likely that a user went off and changed a different part of the system which could affect the return value of the complex_operation. Remember that all the `||= operator` is doing is *assigning the value to the left, the value on the right if it is nil*.

With all that out of the way, something that may confuse is that the above is not entirely correct, the `||= operator` has hidden extra behaviour, it will actually *assign the value to the left, if the value on the right is falsey*. In this situation 'falsey' means: It simply means if is nil or false. So for example if the result of a really complex operation was false and this is what you wanted to use for memoization then you would have an added effect.

In the case where:

```
x = false

(1..10).each do |y|
  x ||= y == 5
end

x => true
```

x can become true. It's the same if x was initially nil. This example is a bit contrived because, if you initially set x to anything else, e.g. 1, 4, 5, 6, "a" or anything that isn't nil or false then x will remain that value.

Hopefully this post has given you a bit more of an insight into why it may not just be easy to cache the result of an API query with `||=`. This is why in Rails we have fuller cacheing mechanisms like:

```
def api_result
  Rails.cache.fetch("#{cache_key_with_version}/api_endpoint", expires_in: 12.hours) do
    UserInfo::API.paginated_user_music_likes(self.id)
  end
end
```

These give you a bit more functionality in terms of cacheing, because these caches have expiry mechanisms.

After all the two hardest things in Computer Science, are cache invalidation and naming things.