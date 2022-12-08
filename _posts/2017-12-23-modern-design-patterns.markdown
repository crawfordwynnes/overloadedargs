---
layout: post
title:  "Modern Design Patterns"
date:   2017-12-23 11:04:08 +0100
categories: jekyll update
---
The original Gang Of Four, Design Patterns was released in 1994 and it paved the way for software development methodologies at the time. Modern programming languages contain many syntactic nuances but nearly all languages can lead to programs which can show a number of different patterns. The original design patterns are still very relevant and some of them are resisted here, but this is a list of patterns which you may find relevant to reference to whilst learning design patterns.

## Decorator

Specifically used in Rails to simplify Logic between the Controller and View layers. They can help pass complex objects to the view layers so that there is minimal logic in the views or controllers, the kind of code you may see include wrappers or replacements for helpers that can change the formatting of data ready for display, such as I18N \(internationalization\) helpers. In an abstract form they may be described as one of th V's in MVVC or very loosely part of template engines.

## Proxy

This pattern is everywhere and is specifically relevant to web servers. Considering that modern web servers will run in parallel with multiple processes that proxy to another web server it is quite easy to see that the pattern finds it way growing into normal classes for development with classes that can call methods without all of the required data. Specifically for example a Proxy for a Car Object could be a Car Runner Object where the Car Runner object receives calls for the Car Object and then decides to send method calls to the Car using some of the data or transformed data. ActiveRecord in Rails has a proxy instance that allows us to do method chaining, which is the one that is interacted with.

## Singleton

This pattern specifies that only one instance of the class should be available at runtime. If an instance of an object is created then another can not be created unless an exception of some kind is to be thrown. This can help with memory considerations. It can also help the programmer to understand the reference to the only one type of instance available for that class. 

For example specifying class methods in Ruby can mean opening up the Singleton class (also called the Eigenclass) on a Ruby object to define the methods — Class methods are singleton methods on the Class instance of a class.

## Factory

A Factory usually is a class which once created will create objects of a type of it's choosing. So if you created a Vehicle Factory, it could give you back distinct instances of Vehicle classes like a Car class or a Lorry class. Starting with a main code base and having a piece of code which grows past a line count limit is a sign that the code could be split up using a factory pattern.

## Interface

An interface is an extremely useful technique for software developers who want to mock out what the method calls for a class should look like. The actual interface should contain method names but no actual logic or implementation. When a class is created through inheritance or polymorphism it's derived classes will share these common method names, which can be overloaded. The interface class can even go as far as e.g. in Ruby to raise a NotImplementedError to show the programmer that the class is an interface class. A somewhat similar concept is seen with function prototypes in languages like C. One loosely related convention is to use Base classes to create abstract classes which are then intended to create a concrete class.

## Reactor

Reactors run in an infinite loop and will listen for method calls and respond to them as appropriate. They have been used extensively in Mobile phone development and main classes are derived from the first reactor loop which can be the entry point to the application. Reactors can be useful in asynchronous programming and an analogy can be seen in the way web servers run in a loop listening for HTTP requests waiting for calls to be translated at the routes layer.

## Wrapper

A wrapper or a Shim is usually a lightweight piece of code similar to a Proxy but not containing much if any extra logic at all. It's job is to take data from one format and call the derived methods from other classes. Like writing classes which can translate method calls from one format to another, or from one language to another, redirecting an API or surrounding a class which relies on one format with methods which simplify the method calls to the environments language, objects and method formats. 

## Observer

Observers are very neat pieces of code which wait and listen to state changes on an objects data by intercepting method calls. They are not appropriate in most cases and opinion has changed in regard to their use in larger code bases. They are very useful for triggering events in other classes once data has entered into a system and passed validation.

## Command

The command pattern is a very specific pattern useful for creating classes with a similar simple interface. To do this you specify an Execute/Run method and define a DSL for interacting with parameters passed to a class.