---
layout: single
title:  "Hobgoblins and Data Stores"
tags:
  - kafka
  - software architecture
---

*"A foolish consistency is the hobgoblin of little minds, adored by
little statesmen and philosophers and divines.\" - Emerson*

Continuing with my ideas from the [other
day](/2014/05/26/A-Foolish-Consistency.html), I wanted to move
from the abstract to the more concrete, which is to say that I wanted to
talk about what such a system would look like.

To begin with, let\'s assume that this system is a fairly large-scale
distributed system, so that, were we to design it the way such things
are typically designed these days, we would probably have some sort of
cache between our business logic and the data layer (say, Redis or
Memcached). The back-end data model is probably normalized and very
relational, but since we\'re having to cache the data for the business
layer, the data is mostly accessed via a key which is derived from the
primary indexes of the table(s) being used. In other words, from the
point of view of the business layer, the data layer is just a key/value
store.

This becomes even more true if the data becomes large enough to require
sharding the database. Now, to find the proper data store/cache for the
data, you take the key you use for the cache, hash it, and then use some
algorithm to take you from the hash to the actual data store.

So now we have the business layer dealing with the data in terms of
key/value pairs, talking directly only with the cache in most cases
(except the case of a cache miss, which you want to be rare and handled
transparently anyway), but we\'re still having to send the data to our
relational database, paying the network and transactional cost so that
we have a \"system of record\" (and, of course, can do our reports).

#### Nonsense and Non-sensibility {#nonsenseandnonsensibility}

This doesn\'t really make much sense to me, especially when part of the
cost of this \"system of record\" is strong coupling within the system.

Let\'s think instead about where we want to get to, from a scalability
perspective. Ultimately, what we want, I think, is a system that has
been componentized at the architectural level - where our component is
an instance of our front-end/business/data layer, and we scale by adding
new instances of these. Further, we stop worrying about the whole thing
being consistent, and instead accept that it may be *eventually
consistent* instead.

To do this, we\'d need to decouple our persistent data store from each
of those instances, so that each instance\'s data layer can
read/write/update the underlying data without being coupled to it.

So, let\'s try this: a persistent data store of some sort linked to the
instance\'s data layer via a pub/sub framework of some sort. Each
instance receives updates about the data from the framework, and in turn
updates the data store via the framework. We would need to guarantee
eventual delivery via the framework, but it wouldn\'t be synchronous,
and, in cases of network fragmentation, might not arrive for quite a
while.

Each instance would use the cache key as the subscription topic, and,
once they subscribed, would receive all updates from all components
posted to the topic, so that, for instance, if another instance updated
the data they would get that message directly, rather than having to
access the persistent data store to get it.

There\'s only one rub: how do we bootstrap such a system? How do we seed
the initial data in the pub/sub framework on startup? We probably don\'t
want something publishing every \"row\" of our persistent store on
startup, for instance, so how do we manage getting the initial data out.

What we need is the ability for some component to be able to detect when
someone first subscribes to a new topic, and then populate that topic
with the data from the persistent store when that happens.

#### Kafka

One way to do this is to use something like [Apache
Kafka](http://kafka.apache.org). I won\'t give a thorough description of
how it works, but, basically, it uses
[Zookeeper](http://zookeeper.apache.org) to manage the subscriptions,
which means that you can ask Zookeeper to let you know when a new topic
is created.

So now, we have our persistent data store (which can be, really,
anything) fronted by a component whose whole job is to listen for new
topics and publish the corresponding initial data. Each instance\'s data
layer simply subscribes to the proper topic when a new request from the
business layer comes in, and updates its cache of the current state of
the data from the updates on the topic, as well as publishing any
changes it makes to the data. As instances come online, they bootstrap
from the system so that loss of any individual instance isn\'t
catastrophic.

![](https://www.geekheads.net/content/images/2014/May/Hobgoblins.png)

This architecture becomes very interesting, because it demotes the
persistent data store from being the be-all and end-all of the system to
being just another component. Because it\'s just another component, just
another client of the pub/sub framework, things become much more
flexible. Now, it can be multiple data stores, not just one. They can be
replicated, or sharded, or whatever you want. Since the data layers of
our application are decoupled from them, changing it no longer means
changing the application.

Even better, it means that *the persistent data store can be changed
while the system is running*. You want to shard an existing database?
Bring up a new instance of the database, but have the data publisher
front end only listen for certain keys. Let it populate itself from the
pub/sub network, and then change the data publisher on the original
store to exclude those keys. Voila! You\'re sharded!
