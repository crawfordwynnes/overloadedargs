---
layout: post
title:  "On Pair Programming"
date:   2017-12-19 10:04:08 +0100
categories: jekyll update
---
Just in case, like me, you have been living under a rock for a really long time, here is a reminder that pure pair programming TDD works like this:

"hey, dude, we have to write a program that does X"

"oh, rite, so what should the output be" (codes a test which fails and turns test output 'red')

"yeah well I can make that program work by" (codes a method/file/program which passes the test and turns the test output 'green')

"yeah but you didn't even check that it also has to satisfy these requirements" (codes more tests to make the tests fail).

"ok, here goes actual implementation, help me out here". (codes actual program)

"yeah you definitely missed that edge case and you can refactor that this way" (codes more failing edge case tests, not being so pedantic than an unmaintainable test suite parallelized will take two millenia to run)

So anyway that's just TDD, and if we zoom in a little more to the types of testing methodologies, usually it's much better to have a good spattering of different types of tests, Unit tests and Integration tests, with some extra acceptance tests/api tests as appropriate. (There are tons of types of tests) https://www.guru99.com/types-of-software-testing.html

So with Rails apps we can follow red-green-refactor using acceptance testing frameworks. This works by writing some failing tests in the business domain level which can help bring non-software type people and clients into the picture. Then writing some failing controller specs and when these fail either mocking calls to unit tests or writing the actual code for unit tests and allowing the functional tests to drive the unit tests, ala integration testing. Also remembering to mock all external calls to other web services where required. 

As a side note using A/B testing for product changes in it's purest form leads to XDD, which is hypothesising about a change to a business model or module or function, implementing changes in an A/B or split fashion and measuring the results. Resolving the most efficient results leads to the most optimum startup and allows a business to fail early at multiple stages so that it can keep running. 

Dynamic XDD is applying this process in real time and resolving split tests to customer segments as data improves. For example if you showed a shopping cart with a quantity button and without a quantity button and got more overall conversions and more profit from one of them, you would show more of your customers the better option as the experiment progressed.

Update: Now there is also ATDD and STDD (Acceptance Test-Driven Development and Story Test-Driven Development; using Cucumber's Natural Language-like DSL).