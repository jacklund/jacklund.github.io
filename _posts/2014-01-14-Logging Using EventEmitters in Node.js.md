---
layout: single
title:  "Logging Using EventEmitters in Node.js"
---

I\'ve been working a lot in node.js lately for a work project.
Javascript as a language is an odd duck ([pun not
intended](http://en.wikipedia.org/wiki/Duck_typing#In_JavaScript)).
It\'s got all these incredibly powerful features - dynamic typing,
inheritance-by-prototype, functions as first-class objects - but it has
some really odd anachronisms, including having to use the C-style
`for (var i = 0; i < length; i++)` loop to iterate over arrays. Node
adds a lot to the language as well, such as
[EventEmitters](http://nodejs.org/api/events.html), which are very
powerful. This afternoon I found a nifty new use for them: logging.

One of the issues that always seems to come up is the fact that logging
is one of those things that is both local and global - it\'s local, in
that you want to do the logging at the place where the event you\'re
logging occurs, but global in that you want your logging configured
globally - you don\'t want each and every class/module to have to
\"know\" about logging. One consequence of this, especially with respect
to testing, is that you end up having to configure loggers for your unit
tests, which, in a way, makes them no longer \"unit\" tests at all.
Optimally, what you want is a situation where you\'re logging locally,
but if logging hasn\'t been set up by anyone, the logs just go into
`/dev/null`. Basically, you want your logging to involve sending out
logging \"events\", which are either captured by something, or not.
EventEmitters give that to you for free.

All EventEmitters are, for those of you unfamiliar with them (but
familiar with OO terminology) are an implementation of the [observer
pattern](http://en.wikipedia.org/wiki/Observer_pattern), but a really
lightweight and easy to use one. I won\'t go into details on how it
works, if you\'re interested, look at the docs (or, even better, check
out [eventemitter2](https://github.com/hij1nx/EventEmitter2), which adds
wildcards and namespaces to it). What I want to talk about is how to
leverage it to make logging nicer.

For instance, if you have a logging package that you\'re using (I\'m
using [winston](https://github.com/flatiron/winston)), you just create a
module containing a class that is an `EventEmitter`:

    var EventEmitter = require('events').EventEmitter;
    var util = require('util');

    function LogEmitter() {
      EventEmitter.call(this);
    }

    util.inherit(LogEmitter, EventEmitter);

Then, create an instance of your emitter, and you make your logging
method emit an event:

    var logEmitter = new LogEmitter();

    module.exports.log = function(level, message) {
      logEmitter.emit('logging', level, message);
    }

This just emits a `logging` event when `log()` is called, passing the
parameters along.

Finally, you also have something listening if someone initializes
logging:

    var initialized = false;
    module.exports.initialize = function() {
      if (!initialized) {
        initialized = true;
        logEmitter.on('logging', function(level, message) {
          // Log the message through your logging package here
        });
      }
    }

Then, you\'re good to go. Just distribute `logging.log()` calls
throughout your code. If something in the code calls
`logging.initialize()`, great, your messages get logged. If not (like,
say, in a unit test), the messages go into the bitbucket.
