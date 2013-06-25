---
layout: post
title: Scala Is Disappointing
date: 2013-06-25 00:15
comments: true
categories: scala eclipse sbt
---
So, I've been playing around with Scala, and, while I'm quite impressed with the language syntax, it's a real pain-in-the-ass to deal with. To begin with, the various versions (2.7, 2.8, 2.9., 2.10) are [binary incompatible](http://lift.la/scalas-version-fragility-make-the-enterprise), which is really annoying. I understand that the language is still in development, but you would think they'd have the core of the language all worked out by now.

I understand the [reasons behind it](http://suereth.blogspot.com/2011/12/scala-fresh-is-alive.html): they're still making changes to the base traits which causes all the objects to change. But, really, this is one of those things that every language (well, every language with a base class or interface) goes through. These are the things you go through, however, when you're doing the pre-1.0 releases of the language, not 2.x; at most, you make these changes when you transition between major revisions - from 2.x to 3.0, for instance - *not* between minor revisions.

What concerns me with this, besides the PIA factor, is that a) the language developers don't seem to think this is a problem, and b) that it indicates a tendency to want to "fiddle" with the language. I understand the desire to "fiddle", it's one of those things that every software developer goes through, the desire to "tweak it just a little bit more" after it's released. It's also one of those things that mature software developers grow out of. "The perfect is the enemy of the good".

They don't seem to realize that this is going to make it *really* hard for it to be taken seriously as a general-purpose language. When you're trying to make a business case for using a new language, one of the things that you have to demonstrate is that the language is mature enough to not change out from under you. Scala is changing constantly, and the Scala developers seem to think this is a virtue, rather than a problem.

Why is this a problem? Well, the fact that you have Scala libraries having to label themselves with the version of Scala they're compiled against is an immediate indication. If I'm trying to do a project that's dependent on libraries, I now have to make absolutely sure that there is some version of Scala that they're all compatible with, and then use that version for my project, rather than using the version based on the feature set I need. What's more, if there is no overlap - if one of my dependencies is only compatible with, say, 2.9, and another only builds on 2.7, then I'm hosed. I either have to try to build one or the other on a different version of the compiler, which may or may not work, or use different libraries.

The other bit of craziness is the Eclipse Scala plugin can't change versions, and, you can't have more than one version running at a time. Even more fun: the only Scala plugins for Eclipse Juno are for 2.9 and 2.10 - if you have to use 2.8, you're either going to have to use Indigo, or cross-compile to a newer version of Scala.

All in all, I'd have to say that, as much as I like the idea of Scala, I can't see using it for anything significant until it becomes more stable.