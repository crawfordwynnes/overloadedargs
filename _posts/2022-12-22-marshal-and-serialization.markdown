---
layout: post
title:  "Marshalling and Serialization in Ruby"
date:   2023-11-13 13:30:00 +0100
categories: jekyll update
---

This is a quick post about a little known feature of Ruby that is extremely powerful. It has it's roots in serializing data. 

What it allows you to do is save your Ruby Objects directly into a byte stream, this can then be stored outside the current scope and then written disk, then the object can be immediately loaded again in another part of your program or a different program.

You use it like:

{% highlight ruby %}

example_object = ExampleObject.new(args)
example = Marshal.dump(example_object)

{% endhighlight %}

and then load it elsewhere back into your program with:

{% highlight ruby %}

example_object = Marshal.load(example)

{% endhighlight %}

For example you can combine this with IO to save your Object to a file, for instance you can, if you needed to, save an Object to disk in a Rails request and then get the restored object between requests.

You can also use it within a Ruby program to load a precomputed Object into memory but this probably would suggest that you need another way to save the Object. I suppose an example of this would be that you wanted your class to initialise a large Object explicitly each time but you didn't want to have to compute it each time.

There may be considerations for being able to load the Object between threads or as you are serializing the data you would end up passing references to a serialized Object which may or may not be more efficient.

### Other formats

Other programming languages have a vaguely similar idea of outputting objects to STDIO so they can be read directly as objects in other programs, for example by redirecting output with `|` which saves disk space. This kind of pattern is following the Unix philosophy of writing small modular programs. However unless your doing something extremely low level then I think it would make more sense to choose an alternative format to serialize to, this could be anything from JSON, XML, TSV, YAML or CSON and many others.

---

In Computer Science serialization (marshalling) and deserialization (unmarshalling) are the process of transforming data to store or send as a stream, this is why that it covers many different formats and for example if you deserialize a YAML Object then you would be reading the YAML file and then parsing it. 

I have seen that Sidekiq is sometimes used as a reference example for serialization. This is because Sidekiq (a background processer) is run as a standalone program in the background, your main program loads the DSL and lib for Sidekiq into the program you want to send background jobs from and it creates custom serialized JSON objects which get sent to a persistence layer that is supported, something like Redis or Psql. A quick note if you are trying this out is that tools like foreman can be useful to run your Rails server and Sidekiq processes at the same time, also you can check if your Redis instance is set up to persist for the length that your jobs are set to. Finally it can be said that different formats can be quite complex so it may be easier to stick with something like JSON.