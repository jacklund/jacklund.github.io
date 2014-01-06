---
layout: post
title: "Inheritance in Functional Languages"
date: 2014-01-05 11:31
comments: true
categories: node.js javascript inheritance functional
---

There's a tendency amongst proponents of [functional](http://en.wikipedia.org/wiki/Functional_programming) languages, like Javascript, to consider inheritance an anachronism of older (and, by implication, worse) OO languages and OO design. One example is this [discussion](http://book.mixu.net/node/ch6.html) by [Mikito Takada](http://mixu.net). This is what he says:

> I think classical inheritance is in most cases an antipattern in Javascript. Why?

> There are two reasons to have inheritance:

> 1. to support polymorphism in languages that do not have dynamic typing, like C++. The class acts as an interface specification for a type. This provides the benefit of being able to replace one class with another (such as a function that operates on a Shape that can accept subclasses like Circle). However, Javascript doesn't require you to do this: the only thing that matters is that a method or property can be looked up when called/accessed.
2. to reuse code. Here the theory is that you can reuse code by having a hierarchy of items that go from an abstract implementation to a more specific one, and you can thus define multiple subclasses in terms of a parent class. This is sometimes useful, but not that often.

> The disadvantages of inheritance are:

> 1. Nonstandard, hidden implementations of classical inheritance. Javascript doesn't have a builtin way to define class inheritance, so people invent their own ones. These implementations are similar to each other, but differ in subtle ways.
2. Deep inheritance trees. Subclasses are aware of the implementation details of their superclasses, which means that you need to understand both. What you see in the code is not what you get: instead, parts of an implementation are defined in the subclass and the rest are defined piecemeal in the inheritance tree. The implementation is thus sprinkled over multiple files, and you have to mentally recombine those to understand the actual behavior.

> I favor composition over inheritance:

> * Composition - Functionality of an object is made up of an aggregate of different classes by containing instances of other objects.
> * Inheritance - Functionality of an object is made up of it's own functionality plus functionality from its parent classes.

Now, I don't disagree with him in principle here - inheritance *can* be an anti-pattern when overused, which it is, even in the world of OO languages (I've even seen it implemented, and then overused, in C, which, well, you've really got to see to believe). However, that being said, I think what concerns me about this quote in particular, and the corresponding attitude amongst those in the function-programming community in general, concerns a basic misunderstanding of what inheritance ("classical" or prototypical) is, and a further misunderstanding of one of the fundamental ideas behind software design.

## What is Inheritance For?
To begin with, we need to distinguish clearly between interface inheritance and implementation inheritance, because they are very different things. Interface inheritance is about defining a contract for an interface - no sharing of implementation is involved at all. It's basically how you achieve both polymorphism and a well-defined interface in statically-typed languages. Since it doesn't share any code, talking about "composition vs. inheritance" or "code reuse" really has nothing to do with it. What most such discussions are really talking about is implementation inheritance.

The question then becomes: what is implementation-type inheritance for? After all, it's fairly trivial to see that you can achieve the same thing with composition or, in languages that support it, mixins. Why have it at all? The answer has to do with one of the fundamental ideas behind the design of anything: communication.

## Design is Communication
In his book [*The Design of Everyday Things*](http://en.wikipedia.org/wiki/The_Design_of_Everyday_Things) (which, IMHO, should be required reading for any software developer), Donald Norman gives an example of the design of a door, and what it communicates to those who want to use the door. If the door swings only one way, then placing a pull handle on the side that opens in and a push plate on the side that opens out communicates, almost at a subconscious level, what the person approaching the door needs to do in order to open the door.

Similarly, the software we write should be communicating something about its use to the next developer who reads it. That developer, if we do our job right, should have an almost intuitive understanding of how to use or modify the code with very little effort. What does this have to do with inheritance? Glad you asked.

On a superficial level, what inheritance does is communicate what classes belong to a particular group (the classical "is-a" test for inheritance). But, what inheritance *really* communicates, and what differentiates it from composition, is that it tells you what behavior is *required* for a class to be a particular thing. In other words, *inheritance communicates the required behavior for a class, whereas composition communicates optional behavior*.

To see this, imagine that you have to write a custom implementation for some third-party framework which you've never seen the code for before. It already has classes that have other implementations for the same sort of thing you're trying to accomplish, so you crack one of them open to see how they did it. You immediately notice that, in this one implementation, they're passing in a reference to another class and using it in their implementation. Would your immediate conclusion (before looking at anything else) be that you would need to also use that same class in your implementation, or would you think that it's probably just being used by this particular implementation? In other words, would you think this was required behavior for any class trying to implement this type of class, or would you think it was optional? What if you saw that it was being inherited instead of being composed?

As another example (and, really, the one that brought this to my attention in the first place), I'm attempting to write a [DAO]() framework in Node.js for a set of applications my company is building. This framework would have different implementations depending on what underlying data store was being used. However, one of the things I wanted it to be able to do is to read from an in-memory cache, if one is provided, before going to the data store. This is behavior I wanted to be part of the framework and any DAO class within it. The question becomes, should I use inheritance or composition? In my opinion, using composition would be a mistake, because it would communicate that this is optional behavior that might not be used by some implementations, whereas I want it to be used by all. Hence, I would use inheritance.

This sort of thing, this ability to communicate the functionality of code intuitively, is the difference, IMHO, between well-designed code (i.e., code that takes little or no effort to modify or augment) and badly-designed code (i.e. code that is a pain to modify, and that you have to read through extensively to use).

## When Not To Use Inheritance
Mixu is very much right when he talks about the misuse of inheritance - people have been using it inappropriately for pretty much the entire time it's been available. One anti-pattern for inheritance is, of course, using it for code reuse between classes which have no real connection to each other. Another is to use it as a dumping ground for common behavior - this is typically seen when you have a base class that exists for valid reasons, but then lazy programmers use it to add common functionality which should be extracted into a composed or utility class. Those are all valid anti-patterns of inheritance use.

However, the reverse case is also true - it is an anti-pattern to use composition where inheritance is called for. The "code smell" for inappropriate composition is when you see the same boilerplate code around the use of a particular composed class in a bunch of classes doing the same thing - a sure sign of using composition where inheritance is required.

I am also concerned where he says that he uses inheritance, "but not that often". That would imply, to me, that either he almost never writes polymorphic code, or if he does, the code almost never shares behavior. In my experience, polymorphic classes often *do* share behavior, at least at some level, and so for a developer to say he doesn't use inheritance very often implies to me that he might be either unconsciously writing a lot of boilerplate code, or making his code bend over backwards in order to avoid using the dreaded inheritance. Either way, I'm sceptical.

## Inheritance in Javascript
That being said, I think Mixu has a very good point about the design of Javascript inheritance - it sucks. It's shoddy, it invites, as he says, nonstandard implementations, and is error-prone. Even Node.js' solution - `util.inherits()` - is kludgey at best. It should have been made a keyword in the language so that, again, it's clear what's going on, rather than having to hunt around for certain coding structures which imply it. However, that's a problem with Javascript, not inheritance.