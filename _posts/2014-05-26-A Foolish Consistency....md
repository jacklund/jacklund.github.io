---
layout: post
title:  "A Foolish Consistency..."
---

How do you scale a computer system?

It seems like such a simple question. Of course, to a certain extent, it
is - if you\'ve designed the system properly up front. By this I mean
that, if you\'ve broken the overall system down into components which
are a)
[decoupled](http://en.wikipedia.org/wiki/Coupling_(computer_science)) as
much as possible, and b) as internally
[cohesive](http://en.wikipedia.org/wiki/Cohesion_(computer_science)) as
possible.

However, when it comes to distributed computer systems - ones with, say,
a UI, a bunch of business logic, and a database - it somehow becomes
much more complicated. We know how to scale the UI and business layers,
but scaling the database always becomes\...messy.

I\'ve recently been thinking a lot about why this is, spurred, in
particular, by an interesting
[video](http://channel9.msdn.com/Shows/Going+Deep/Hewitt-Meijer-and-Szyperski-The-Actor-Model-everything-you-wanted-to-know-but-were-afraid-to-ask)
of a discussion between Carl Hewitt, Erik Meijer and Clemens Szyperski.

#### Consistently Inconsistent {#consistentlyinconsistent}

The discussion is mostly about Dr. Hewitt\'s actor model of computation
- which is interesting in and of itself - but the thing that really
caught my attention was an almost offhand comment he made about
relational databases being a problem in that they are trying to enforce
consistency in an inherently non-consistent situation.

To think about this, we first need to figure out what we mean by
\"consistent\" in this context: we would regard a system as consistent
when all parts of the system agree on the state of the system at any
given time. Another way of putting this is that the system is
transactionally atomic at the global scale.

Or, to put it in other words, it\'s a Turing machine.

#### Where Do I Put the Paper Tape? {#wheredoiputthepapertape}

If you\'ll recall, a Turing machine is a model of computation in which
the state of a computer system is recorded on paper tape through a
series of consecutive operations. Turing machines are the model computer
scientists have used for years to model computer systems. Dr. Hewitt\'s
point (and I think he\'s right here) is that they aren\'t Turing
machines at all, and that the Turing machine model is entirely the wrong
model to use.

Why? Well, because modern distributed systems have two things Turing
machines don\'t have: *concurrency* and *latency*. Concurrency is, of
course, the ability to have two or more tasks executing at the same time
(this is slightly different than parallelism, in that the tasks don\'t
have to be executing *simultaneously*, but can just be taking turns
executing). Latency is the idea that new information is not available to
all parts of the system simultaneously. However, by modeling systems as
Turing machines, you\'re assuming that a) the system has a single state
that b) is known simultaneously throughout the system; in other words,
the system is *consistent*.

For example, if our system is a banking system, we would want it to be
consistent in that the balance of a given account, for example, is
always the same everywhere in the system. If we get a simultaneous
deposit and withdrawal, the system will somehow process each and arrive
at the correct balance all at once.

How do we achieve this magic in systems that may span continents, for
instance? Databases. Databases are the transactional glue that holds
this together. We treat the database as the \"system of record\", and,
since it\'s transactional, we use it to enforce the transactionality of
our system as a whole.

Which is where everything falls apart.

#### Modern Systems as an Extension of the Database {#modernsystemsasanextensionofthedatabase}

Because, what this means is that you\'re limiting the scale of the
system to how well you can scale your database. Rather than increasing
cohesion and reducing coupling, you\'re instead coupling your whole
system together - tightly - through the database, because all parts of
the system have to ensure that the system of record - the database -
gets to the proper, consistent, state before continuing.

You can push the problem off, of course - there are scads of strategies
for doing so. But it\'s still ignoring the fundamental problem, which is
that you\'re trying to scale a fundamentally coupled system (or, to put
it another, equivalent, way: you\'re trying to make an inherently
inconsistent system consistent).

For instance, let\'s take a typical large-scale distributed system, with
a UI layer, a business layer, and a database, distributed over several
machines, maybe even over several data centers on different continents,
with, however, a single, logical database which is the system of record.

Of course, since the network latency plus the database locking is a
horrible performance hit, your first \"optimization\" is to cache the
data locally, creating a caching key by hashing some combination of data
values based on the table indexes. But, of course, since you don\'t want
the system to get into an inconsistent state, you find ways to make sure
the cache and the database are in agreement - write-through caches, for
example, or cache replication - which are complicated to maintain in
production, but necessary for consistency.

Then the next point of pain is that the database is too large, so you
shard the database. But, again, since you want your system to be
consistent, you have to make sure that each part of the system has a way
of accessing the correct data shard for the correct data, which further
complicates the situation. So you suddenly find yourself adding a lot of
complexity around keeping your database updated with the correct
information. And this is not even thinking about the possibility of
[network fragmentation](http://en.wikipedia.org/wiki/CAP_theorem).

#### Jewel of Denial {#jewelofdenial}

What to do? Well, to begin with, we can stop being in denial about the
consistency of our systems. Once we realize that pursuing consistency is
a foolish endeavor, and that *eventual* consistency is the best we can
hope for, it becomes relatively simple: design our systems the way they
actually *should* be designed - decoupled cohesive subsystems which will
come to some agreement about the state of the overall system
*eventually*. We need to understand that if one part of the system tells
a user that her balance is X because it hasn\'t gotten the message from
another part that it\'s actually Y it\'s not the end of the world,
because users understand that things can\'t happen instantaneously.

In the case of the application above, we let each local system keep
track of its own ideas of the state of the system-as-a-whole, while
providing a mechanism for each subsystem to communicate its idea of the
current state with each other subsystem, and some way of the whole
system coming to some consensus of what the actual state is. Thus, when
subsystem A gets a deposit, while subsystem B gets a withdrawal on the
same account, each will have a different idea of the balance on the
account until they update each other, at which time both will end up
with the correct final balance. In return for this temporary
inconsistency we will get much simpler systems which are both more
performant (because of not having to come to immediate consensus on the
state of the system, with the corresponding network latencies and lock
contention) and more scalable (because the subsystems are truly
decoupled from each other).
