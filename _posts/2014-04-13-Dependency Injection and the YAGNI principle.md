---
layout: post
title:  "Dependency Injection and the YAGNI principle"
---

One of my new personal projects involves writing an application using
[Node.js](http://nodejs.org) and [MongoDB](http://www.mongodb.org).
It\'s going to have a
[RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer)
interface for the services, and MongoDB as the database (I haven\'t
decided about what I\'m going to use for the front-end, but
[Angular.js](http://angularjs.org) is a strong contender). I had looked
at using [mean.io](http://www.mean.io/) to build it, but after playing
with it for a while, it seemed to bring in too many things I wouldn\'t
end up needing, so I decided to build it all from scratch using
[Restify](https://github.com/mcavage/node-restify) for the RESTful
services.

I\'m writing it in an
[MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)-ish
way (really, since it\'s REST, it\'s more like MRC - Model - Route -
Controller) where the top-level module requires routing modules from
whatever Javascript files are in a particular directory (`lib/routes`),
and calls them as functions, passing in the Restify server instance so
the routes can be set up. Each route would call a corresponding
\"controller\", which would bring in a corresponding \"model\", and set
up the functions that each route would use to do its thing. So, the
question became: how do I pass the database connection down to the
model?

When I looked at how mean.io does things, I noticed that they use a
dependency injection package called
\"[dependable](https://github.com/idottv/dependable)\". The way it works
is that you create a \"container\" using dependable, and register
dependencies in it, then the module that needs it would grab the
container and read the dependencies out of it.

First of all, this *isn\'t* dependency injection, because the module
needing the dependencies has to actively retrieve the dependencies from
a container, rather than having them *injected* (hence the term
\"dependency *injection*\"). Of course, it doesn\'t really matter what
you call it, the package performs the basic function well - removing the
coupling between the module creating the dependency and the module using
it. However, I question whether it makes sense for the mean.io stack to
use it in this case, for the simple reason that I believe this is a case
where coupling is fine, in that the top-level module is already coupled
to the routing modules, which are coupled to the controllers, which are
coupled to the models, so pretending that they don\'t know anything
about one another is kind of silly.

This speaks to another issue I\'ve seen a lot of (especially in the Java
world), which is a lack of understanding of the role
[YAGNI](http://en.wikipedia.org/wiki/You_aren't_gonna_need_it) (\"You
Ain\'t Gonna Need It\") should play in programming.

One of the most egregious examples of this (which I\'ve been guilty of
myself) is the idea, in Java, that any class which is a dependency of
another class should have an interface which the depending class uses as
a proxy for it. In other words, if I have a class `A` which contains
class `B`, I feel like I have to create a `BInterface` interface which
`A` really contains, but which is implemented using `B`. The idea behind
this is: what if, some day, I create a new class `C` which implements
`BInterface`, but with a different implementation. Then, I don\'t have
to change `A`\'s declaration, I just change where it creates `B` to
create `C`.

This is fundamentally the same idea as the one where you have to
dependency inject everything - you want everything to be perfectly
flexible, so you don\'t have to make changes when the implementation
changes. However, the hidden expense here is that you now have twice as
many classes/modules that you have to manage. Admittedly, half of them
are interfaces, so they don\'t do much, but it\'s still adding a lot of
complexity where it isn\'t needed, because, chances are, YAGNI. And, in
those places where you do need it, creating a new interface is trivial
(especially in something like Eclipse), so you can do it then.

Flexibility *always* comes with a cost. The key to a good design is
knowing where to add flexibility, and where not to. In the case of my
Node.js code, I decided to pass the database connection through the
route, simply because I think it\'s fine that the top-level module
understands that the routes might need to pass it down to their
dependencies - that\'s what having it as a parameter *means* - either
\"I\'m going to need it\", or \"one of my children will need it\".
Either way, it doesn\'t make the design any less clean, and, if
something changes where I *do* need dependency injection, well, I can
always add it later. YAGNI.
