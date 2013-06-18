---
date: 2012-01-12
title: Getting My Office Set Up
layout: post
comments: true
---
Spent today putting together my desk and chair, moving the network stuff (cable modem, wireless router, NAS server) into the furnace room (now server room) in the basement, and setting up my new Mac Mini server, which will be my desktop (I also have a MacBook Pro which I will continue to use).

Discovered an interesting "backdoor" to JBoss' queues - they publish a "netty" port which makes the queues accessible directly, i.e. not through JNDI. The problem is that JBoss 7.0.2 doesn't yet publish the queues remotely via JNDI, and I had wanted to set up the integration tests to send a message to the web service, which would put it on the queue for the EJB, which would invoke a handler which would put it on *another* queue, which the integration test script would then read. That way, the integration test would be truly end-to-end, and we wouldn't have to worry about messages not making it all the way through. That'w when I ran into that little issue with JNDI, but now, this backdoor approach seems to me like a way around that.

The original code, which I modified, is [here](http://code.google.com/p/javasupport/source/browse/branches/hornetq-examples/src/main/java/deng/hornetqexamples/core/NettyJmsClient.java?r=581).
