---
date: 2012-01-16
title: First Day of Work in the New Office
layout: post
comments: true
---
My first day of work in the new office in my basement. Worked out pretty well, although I *do* need to figure out a way to take more frequent breaks, and maybe do something at lunchtime other than sitting.

I've discovered more really cool stuff with [RESTEasy](http://www.jboss.org/resteasy): not only does it automagically convert JSON to java.util.Map<String, Object>, but it also can convert XML input into an org.w3c.dom.Document. Also, they were thoughtful enough to allow you to have different interface methods for different content types for the same URI, which means that I can have data coming in as JSON call one method (the one with a Map<String, Object> in the signature), and XML call another. Really cool.

Also, got my Groovy integration tests working. Since our architecture is basically a RESTful web service putting data onto a queue, and a MDB taking it off and calling multiple handlers to handle it, I just created another handler to dump the data onto a queue which the integration test reads from (remotely), allowing me to make sure the data got all the way through intact. Very nice.
