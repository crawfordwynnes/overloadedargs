---
layout: post
title:  "De Morgan's Laws for Developers"
date:   2024-11-19 12:00:00 +0000
categories: jekyll update
---

Augustus De Morgan was a British Mathematician and Logician, known for De Morgan's Laws used in Computer Science among other fields. His first paper was called "On the structure of the syllogism" in 1846 and he later wrote "Formal Logic", published in 1847. De Morgan's friend George Boole released on the same day "The Mathematical Analysis of Logic" which would at the time be much more important than De Morgan's work.

Through reading De Morgan's Laws, and their numerous interpretations in different areas, for example set theory or logic, there is one which stands out relating to conjunction, disjunction and negation. It states in English:

 * not "A AND B" is the same as "not A OR not B",
 * not "A OR B" is the same as "not A AND not B".

Why is this useful to us as Developers? First and foremost it's important for refactoring, it's also important for programmers who want to agree on a style relating to if statements, particulary in Ruby there can be some cases where the `unless` keyword is overused.

Consider the following example:

`if !(x == y && b == c)`

as well as,

`unless (x == y && b == c)`

these when refactored would be changed to:

`if (x != y || b != c)`

It would be a mistake to convert the top statements into:

`if (x != y && b != c)`

this would be important when refactoring out large nested if statements, the `unless` adds additional complexity, which is why some people and style checkers attempt to minimise it's use.

# Equivalency

The Equivalence operation is interesting to consider when working with Logic because it can easily be confused with the AND operation. For example in the statement:

`(w == x && y == z)`

We could easily think that the && represented equivalence. We would need to look a bit more closely at what was happening here in terms of logic. In this case if w == x evaluates to true AND y == z evaluates to true then the statement would be satisfied. 

Actual equivalence is slightly more complicated, to get equivalence we would also want the comparisons 
to be true if both sides of the statement were the same, this includes the comparison when the statement is reduced to false == false.

To get equivalence we can use the Xor operator so we get:

`!(false ^ false)`

which would give us true when we used statements that either evaluate to *both* true or *both* false.

# JavaScript Comparisons

In JavaScript it is well known that there are two operators for comparison, We have the 

`===` operator, which checks not only that the values of the Objects are the same but also the Objects themselves. In Ruby, we can get object ids, by using the `object_id` method, Ruby used the `===` operator in `when` clauses, also named *case subsumtion* operator.

If we want to do a comparison not including the object_id, we use the normal comparison operator, but in JavaScript most of the time we do want to use the `===` operator (strict comparison, without type comparisons) to avoid incorrect comparisons.

# Nils

Why is this all important to understand? It's useful because when writing code we want to avoid situations which result in comparing results and giving incorrect solutions. In practise it dosen't actually happen that much, but in particular focusing on the result of the .nil? method will give you a better understanding:

e.g. 

``` nil == false => false ```

``` nil == true => false```

``` nil.nil? => true```

``` x = 0; x.nil? => false ```

# Foobar

Sometimes when discussing functions and return results, programmers use the `foobar` notation. This basically means stating how the function interacts with it's args and what it returns.

e.g. 

'calling foo with bar returns bar squared'

'or calling foo with bar has no unintended side effects'

'or calling foo with bar calls bar2 with the reference to the function foo, which creates an asynchronous Promise'

Any combination of foo and bar can be used.

# !!

Just to finish of this post which is quite heavy on logic and comparison operators there is an interesting use case for the double negation in Ruby which you may come across. 

If we say `!!x`, then x will evaluate to true if it is not nil or false, otherwise it will be false.