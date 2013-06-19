---
date: 2013-05-06 21:43  
title: VerifyError with JDK 7  
categories: java jdk7 cobertura  
layout: post
comments: true
---
We had been using nothing higher than JDK 6 with our project up until now, but when I built a new build machine using Fedora 18, JDK 7 was our only real option (okay, that and JDK 5). However, once I got things building using ant, I got the following cryptic error in the unit tests:

    java.lang.VerifyError: Expecting a stackmap frame at branch target 41 in method com.m2mci.correlation.CorrelationId.equals(Ljava/lang/Object;)Z at offset 24

After some [digging](http://www.javacraft.org/2012/07/cobertura-with-jdk7.html), I found out that the kind folks at Oracle/Sun had changed the bytecode in JDK 7 such that there was a verifier frame now included where there wasn't before. This is fine as long as everything knows about this, but we've been using [Cobertura](http://cobertura.sourceforge.net/) for code coverage, which instruments the bytecode, and which apparently doesn't know about the fancy new verifier stack frame. What's worse, they don't seem (from what I can tell from their website) to have any interest in adding it any time soon.

So, what to do? Our two alternatives are a) drop Cobertura, or b) include the JVM argument ``-XX:-UseSplitVerifier`` every time we run our code. To me, the former seems the better way to go, especially since we're not really _using_ code coverage right now (we probably should, but for now, we're not).
