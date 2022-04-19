---
layout: post
title:  "Kotlin On The Block"
date:   2022-02-22 14:08:08 +0100
categories: jekyll update
---
Today I'm going to outline my brief foray into the world of Kotlin. I guess it was brought on by a nagging feeling of what is this data science paradigm. Well I found out that starting out in data science at least from a hobbyists perspective is quite easy. There are a number of algorithms which can be developed quickly to get a feel of manipulating data. I started with kMeans as it was quite simple and a project can easily be extended to finding the number of centroids, as well as ideas like cMeans or building Voronoi diagrams which are used in countless real world applications including Chemistry.

I decided to use Kotlin because it seemed like quite a mature language with a big following for such a new language and had been cited as a good language for doing data science. Before you start if you haven't learned any language before I would suggest at least building a few basic programs in Java and reading a little bit about functional operations because these can trip you up when learning Kotlin. I also found out quite early on that despite Kotlin being JVM based (full Java interoprability) and having syntax similar to Java and being able to use Java libraries there wasn't really a clear example of reading in files, which will massively help you get over the hurdle of the Hello World to actual program. If you've not used Java before have a brief google about how the JVM speeds up the longer the program runs as it optimises, I've not directly read about the research behind improvements in JVM, but industry has steadily been improving Java for decades and with Java 11 you will have access to countless projects, such as GUI's & Machine Learning.

Some other things about Kotlin: yes it's slightly insignificant but if you come from a Java world then automatically you'll get System.out.println, and it feels familiar which does help a lot. You'll also get access to public and private variables which are missing in a lot of other languages, you will get mutability straight out of the box and you'll get somewhat interesting type matching errors from the compiler. Building small programs in this language I was really blown away by the similarity to Rusts type matching, even though Rust has types like None, Kotlin allows you to do type casting very easily with the <Int> syntax, so for example, even though it has constructs for ArrayList, you can build your own custom multidimensional Arrays and Lists and you'll get a pretty interesting way of instantiating those arrays, something which is kind of enforced (null protection). You use the keywords IntArrayOf or ListOf and you pass some code which can instantiate those arrays for you, there are some similarities, or moreso familiarities in Java with instantiating and allowing the code to be processed in a block like syntax e.g in Java:

{% highlight ruby %}
int[] num[] = new int[10][10]; and
int[][] num={ {1,2}, {1,2}, {1,2}, {1,2}, {1,2} }; 
{% endhighlight %}

Kotlin feels a little light on the ground for documentation about using the language online even though it's more than a brand new language now, but there are new projects being developed and you can publish your projects straight to the maven repository. With new Github workflows I think it will be easy to build custom workflows to process data more easily which will be used for more applications than just having CI, which in the past was more limited to writing custom bash scripts and piping the data around to new scripts.

Anyway the best tutorial for Kotlin looks like it's at https://www.programiz.com/kotlin-programming/
The main language site is also very easy to use and has tons of information, and there are a few books out.

If your in industry and aiming to build a robust data processing platform out of Kotlin with it's endless features such as statically resolving extension functions (Kotlin is statically typed) you should keep in mind that the development specification is still experimental and can change at any time.

Fun, direct pun intended, fact about Kotlin, is that you get primary and secondary constructors with the init keyword and constructor keyword. I think Kotlin is an awesome language from what I've seen so far, and I should say it would make a great addition as a second language or as a switch away from Java. It probably will be easier to use if you've got a feeling for functions like flatMap, for which if you've learn't programming in an imperative fashion will be harder to get your head around. I wonder if there will be a tool like Jenv for Kotlin, next steps for me are looking at testing and build tools like Gradle.