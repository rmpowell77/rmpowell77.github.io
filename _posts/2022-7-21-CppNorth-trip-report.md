---
layout: post
title: CppNorth 2022 Trip Report
---

The first CppNorth conference finished and I wanted to jot down my notes about the event.

## [CppNorth](https://cppnorth.ca)

> CppNorth will bring together local and visiting speakers for three days filled with opportunities to learn, network and discover what is new in the C++ community. 

CppNorth was an event that was held from July 18th to the 20th in Toronto Canada.  It had 3 tracks of talks, with a daily keynote and about 3 hour long sessions per day.  In addition there were 2 days of classes preceding the conference.
  
Toronto is a great city to visit.  In July the weather was on the hotter and muggier side, but it wasn't impossibly unbarable.  The event was held at the [Omni King Edward](https://www.omnihotels.com/hotels/toronto-king-edward).  The hotel staff was exceeding polite and helpful (or were they just Canadian polite...?), which made the event really special.  The food for the Speaker's dinner and the various snack events was quite good.

The venue was a little strange as the main event space for the Keynots was in the Crystal Ballroom, which was on the top floor while the main are for coffee and the other sessions was on the 2nd floor, which created a bit of a bottleneck at the elevators.  But I would usually tak the stairs downward to avoid the crush.

Here are the events I attended:

### Day 1:

#### [Am I a Good Programmer](https://cppnorth2022.sched.com/event/140e7/opening-keynote-kate-gregory-am-i-a-good-programmer)
Speaker: Kate Gregory

The opening Keynote was a great discussion on what makes a programmer good, and what makes good programmers great.  This was not a technical discussion about language mechanics of C++, or library usage.  It was instead more about the philosophy of thinking about how to code and how to sit back and examine the way you approach it.  It would be a great talk for anybody who programs to watch -- it wasn't just for C++ programmers.

#### [C++ Lambda Idioms](https://cppnorth2022.sched.com/event/140eD/c-lambda-idioms)
Speaker: Timur Doumler

Timur's talk was a deep dive into the Lambda programming idioms -- those common patterns that you see people use with Lambdas.  While not a deepdive into how Lambdas work, he gave a great high-level overview of the mechanics that happen under the hood when they are utilized.  The 3 idioms that I think were pretty interesting were the `+` operator idiom to force a cast to an object pointer, the `overload` idiom to create an aggregate of lambdas (very useful for `visit`-ing a `variant`, and "variable template lambda" idiom which would allow you to call partially speciallized functions in a lambda. 

#### [The Power of Compile-Time Resources](https://cppnorth2022.sched.com/event/140fo/the-power-of-compile-time-resources)
Speaker: Jason Turner

This talk caps off the 28 videos Jason has been doing on `constexpr`.  This session focused on how embedding resources can have a direct effect to memory, code and performance side.  The talk centered around the work Jason had done for a vendor of creating a json to C++ converter tool, which takes a JSON file and produces C++ `constexpr` code that can be embedded in the project for both compile time expressions and used directly at runtime.  The example was using it for creating a json schema that allowed for the parsing of the schema at compiletime so that it didn't need to be loaded each time the program would run.

#### [Lets Talk: Extend and Embed Python with C++](https://cppnorth2022.sched.com/event/140ee/lets-talk-extend-and-embed-python-with-c)
Speaker: Rainer Grimm

This discussion was around the best practices on exposing your code to be used in python (Extend) or how to use python directly in your program (Embedded).  Generally there are a number of ways to do it, but pybind11 is the current best way to do it.

#### [Software Engineering Languages](https://cppnorth2022.sched.com/event/140en/software-engineering-languages)
Speaker: Titus Winters

Titus always delivers a thought provoking and engaging talk.  He posed the question, whats the difference between Computer Programming and Software Engineering.  What is the right "Computing Language"?  

> Software Engineering is the Multi-Person construction of Multi-Version programs. - Dave Parnes

Multi-Person programming considerations:

 * Information Density
 * Abstrations
 * Modularity/Sharing

Multi-Version programming considerations:

 * Ease of Refactoring
 * Scope
 * Version Upgrade paths
 * Release "size"/impact
 * Interop

Strong interoperability between multiple versions is powerful.

#### [Movie Night: with Walter Brown](https://cppnorth2022.sched.com/event/140f2/movie-night-with-walter-brown)
Speaker: Walter Brown

This was a tradition where Walter who show clips and movies that he collected.  The highlights were a movie on the various sorting algorithms and a video on [John Vincent Atanasoff](https://en.wikipedia.org/wiki/John_Vincent_Atanasoff) whos work on digital computers preceeded the Eniac.

### Day 2:

#### [Keynote: Chandler Carruth ⚗️🧪Science experiment time!🧪⚗️](https://cppnorth2022.sched.com/event/140f8/keynote-chandler-carruth-nulbscience-experiment-timenulb)
Speaker: Chandler Carruth

An epic talk that announced Carbon, an experiment to answer the question, what comes after C++?  If we look at other languages, there are evolutions:

 `C` -> `C++`

 `Objective-C` -> `Swift`

 `Java` -> `Kotlin`


What is similar on these is that there is a clear effort on interop.

Another consideration of Carbon is the evoluation process.  The culture of development needs to be established early.

> Culture eats strategy for breakfast, technology for lunch, and products for dinner, and soon thereafter everything else too. - Peter Drucker [modified](https://techcrunch.com/2014/04/12/culture-eats-strategy-for-breakfast/)

So is :

 `C++` -> `Carbon`

Time will tell.  It can move quicker. It can try out new ideas.  Is it competition?  No, it's to allow a place to experiement. 

It will have a goverence model set up to consist currently of Chandler Carruth, Richard Smith, and Kate Gregory.


#### [Value Oriented Programming. Part 1: You say you want to write a function](https://cppnorth2022.sched.com/event/140fE/value-oriented-programming-part-1-you-say-you-want-to-write-a-function)
Speaker: Tony Van Eerd

Another excellent talk by Tony.  While the title says Value Oriented Programming, it was more of a focus on how to write functions, how to think of code abstractions, and how the importance of how planning in advance can avoid "Complecting".

"Complecting" is the process where the small, obvious, and "harmless" changes can over time lead to complexity that is impossible to untangle.  One common element that seems to be present in Complecting code is when data is "close at hand" -- when it is so easy to just "grab" something it intertwines the data and solution that makes it very hard to unwind and understand.  By taking steps in advance that abstract these away you make it harder to do the "easy" step, those steps that lead to complexity.  And by that you can help avoid "Complecting".

I fixed it by making it inout.  That's a code smell.

My function returns void.  That's a code smell.

> People aren't ~~wearing enough hats~~ writing enough functions.

#### [Abstraction: the true superpower of C++](https://cppnorth2022.sched.com/event/140fQ/abstraction-the-true-superpower-of-c)
Speaker: Guy Davidson

Guy's talk centered around the layers of abstract that exist in code, and how the ability of C++ to provide mechanism to create them allow better code to emerge.  The art of Abstraction is an "art".  There aren't hard and fast rules, but abilities to create strong layers of abstraction come from experience.

There's the Problem Domain, and the Solution Domain.  By using the correct layer of abstractions we can move things from the Problem Domain to the Solution Domain.  For example, functions are a way to abstract the individual operations that a program executes, allowing the program to focus on higher levels of operations.  It allows things that were part of the problem domain (the individual operations) to move to the solutions domain (the function abstraction boundary).  Modules are the newest form of abstraction we have in C++.  In a similar way they allow us to increase abstraction and move more into the solutions domain.

If you were to look through the [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines) you soon realize that much of the guidelines could be summed up as: "Respect the layers of Abstraction".

#### [Slowing Down to be Faster - C++ and Divisible Algorithms in Real-Time Systems](https://cppnorth2022.sched.com/event/140fc/slowing-down-to-be-faster-c-and-divisible-algorithms-in-real-time-systems)
Speaker: Patrice Roy

When you have to program something that occurs periodically, such as displaying an image at a regular rate, there are points in the program where you do not have anything to execute, and time could be spent doing other work for the system.  This talk centered around techniques to solve this, specifically for realtime systems.  In these systems, any sort of "unbounded" work, such as a malloc or free, is undesireable.  The example Patrice used was doing a runlength encoding of an image.  The solution Patrice outlined shows that it could be unbounded if run naively.  Instead, we create a more complicated interface that avoided allocations, and by using a lambda we pass in a function that the callee function can use to determine from the caller if it can continue to do work.

It is also possible to build this solution with coroutines, but then these predicates need to be stateful.  So while possible to build, it doesn't seem to be the ideal solution.  

#### [The Best Parts of C++](https://cppnorth2022.sched.com/event/14Fnp/the-best-parts-of-c)
Speaker: Jason Turner

Jason presented a whirl wind of different C++ that shows the power and elegence of the language.  In total there were 25 different features that effectively build on top of each other in this talk.  This isn't an exhaustive list of all the features that exist in C++, but a talk about what are the truly great things we have in the language -- the tools that we should all be using when we a programming.

#### [Lightning Talks](https://cppnorth2022.sched.com/event/140g3/lightning-talks)

## Day 3:

#### [Keynote: Tara Walker "Yes! Robotics for the C++ developer"](https://cppnorth2022.sched.com/event/140gC/keynote-tara-walker-yes-robotics-for-the-c-developer)
Speaker: Tara Walker

This was a great introduction to Robotics.  It outlined the current state of the Robotics/IOT programing paradigm, [ROS](https://www.ros.org), which is basically the development and execution environment for people working in Robotics.  In general you develop "modules" that communicate with each other via a Pub/Sub system.  This flexibility allows development of different parts of the system by different teams, and a way to have code interact with each other.

Devlopment of ROS can be done with both physical hardware and simulation environments.  This allows flexibility for develoment without complicated hardware lab setups.  The tooling is still evolving, and the industry seems to be early in development -- the tools still seem a little unstable and "ornery".  In time this industry will develop solid development tools and stronger simulation environments.

#### [Multidimensional C++](https://cppnorth2022.sched.com/event/15uVo/multidimensional-c)
Speaker: Bryce Adelstein Lelbach

> C++ has no reasonable standard for multi-dimensional data

`std::mdspan` is a way to allow consistent access for multi-dimensional data objects, allowing the interopt of functions that operate on MD data to separate the algorithms from the data; instead of having to write your function to work on `EigenData` or `Matrix` or some other custom data object, you can write your function to execute on `std::mdspan`.  You will need to specify how the data is order, what the rank is, and other details, but once done `std::mdspan` allows this data to be accessed consistently.

A challange emerges: `std::mdspan` does not have a `begin` and `end`.  Iterating through the data is a huge challenge because of the complexity of describing the iteration strategy.  And beyond that it does not appear that the current compiler technology can "see through" the current layers to optimize well.  This limitation prevents usage of a large number of algorithms that exist, and requires building a lot of boiler plate for seemingly simple operations like tiling.  Near the end of the talk Bryce proposes a new `Space` concept that could solve this problem.

#### [The Twin Algorithms](https://cppnorth2022.sched.com/event/140gX/the-twin-algorithms)
Speaker: Conor Hoekstra

A truly excellent talk about the relationship between what appear to be two different algorithms, `std::accumulate` and `std::adjacent_difference`, but are actually connected in a number of remarkable ways.  By examining the origins of these algorithsm to their roots in APL we can see quite clearly that these algorithms are related by the "shape" of the symbols that was used to describe them, `/` and `\`.

Conor outlines several brief histories of APL, and shows the deep connection to C++.  This perspective reminds us that learning multiple languages is important -- it gives us perspective on where we've been and also where we are going.  By knowing the origins we can avoid problems such as tying in the names that imply what an algorithm is doing, like how `adjacent_difference` is really scan.  It is also a reminder that learning additional languages makes you a better programmer:

> You can never understand one language until you understand at least two.  - Geoffrey Willans

#### [Closing Keynote: Sean Parent "The Tragedy of C++, Acts One & Two"](https://cppnorth2022.sched.com/event/140gg/closing-keynote-sean-parent-the-tragedy-of-c-acts-one-two)
Speaker: Sean Parent

Sean's talk is sort of a retrospective of a presentation given 10 years ago at C++Now.

The first part of the "3 part" tragedy is the introduction of our main character, C++, a powerful language.  So many things that it has going for it - Tooling, Libraries, STL, Commuity, Value Semantics, Abstraction...  The future seems bright!

But in the second part, we look at the health of C++.  Where could we have been doing better.  Safety and undefined behavior are complicated.  They are hard to reason about, and the specification C++ now means the language is at a point where it cannot be understood by any one person.  We lack the tools for formally verifing the language itself.

Can we ship a better version of C++?  We need goodness.  We need a better language to emerge.

## Summary

This was an excellent conference to attend.  It was an excellent location and venue.  It had the right amount of talks.  It was a perfect length.  It provided a way to get together as a community and talk with fllow programmers.  I'm looking forward to next year already!


