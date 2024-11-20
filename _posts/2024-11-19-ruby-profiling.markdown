---
layout: post
title:  "Ruby Profiling"
date:   2024-11-19 17:00:00 +0000
categories: jekyll update
---

As we know debugging is a skill all in itself, there are many tools to verify and improve the state of your code, including linters, load testing, static analyzers, security analyzers and memory analyzers to detect memory leaks, like [Ruby Memcheck](https://github.com/Shopify/ruby_memcheck) which uses valgrind. There are also a ton of internal and external tools for monitoring your stack, newer ones include k6.io, Loadero, Cypress, Google Lighthouse, Artillery.io and the Autocannon Node module as well as JMeter, NewRelic and Browserstack. There are even tools you can set up yourself on your boxes for monitoring, like munin and monit which you can integrate with CI tools. I've not had a chance to use many of these tools, but there are hundreds and I think that Cypress.io is making a lot of headway because it's aimed at JavaScript.

However if your currently in the phase of development and iterating on building out features then it's much more likely that your looking at the performance and corectness of your code and focussing on reducing the amount of bugs in your system.

## Ruby Benchmarking

The most simple way to look at the time it takes to run your code is to choose parts which you are interested in, which look like they could be improved quite easily and then use the ruby benchmark module. You simply require it with `require 'benchmark'` and then using `Benchmark.realtime` with a block. There is also a Gem called Benchmark which has different methods.

## Pry, Byebug and Debugger

The default debugging tool in ruby is called debugger, you can get access to it by doing a require 'debug', and then at the place where you are interested in squashing that bug you simply add the `debugger` statement. At this point you would consider that it would be quite similar to adding a breakpoint in the browser inspector in your JS code. Once you enter the debugger it will go through the Ruby call stack so you can figure out what's going on, usually you press n to go to the next statement and c to continue on with the current program or to the next debugger statement. 

The recommended debugger for Ruby is not in fact the debugger shipped with Ruby, it is pry-byebug. In fact most
applications use the Pry Gem on it's own, and instead of the debugger statement you can use binding.pry to enter debugging mode at a certain point.

## .irbrc

To use .irbrc we need to create a hidden file which you can use to modify the behaviour of your irb console, for instance this may be a good place to change the behaviour of print to pretty print which you can normally use only with the `pp` command. You can include debuggers or change the output indentation, although to change the output of the Rails console in Rails 7 and above you may need to use an initializer with:

```
Rails.application.console do
end
```

## _

When working with irb and the console you can use the shortcut `_` to assign the result of the last statement to a variable if it's simpler not to run that statement again.

e.g. 

```
def complex_operation(r)
  long_running_method(r)
end

x = 2

complex_operation(2)
z = _

```

and you will get the result of complex_operation(2) in z.