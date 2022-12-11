---
layout: single
title:  "The Ghost in the Machine"
tags:
  - blog
---

So, I\'ve moved my blog over to [Ghost](https://ghost.org) from
[Octopress](http://octopress.org). I did this for a few reasons.

My main reason for wanting to move away from Octopress was that updating
my blog was getting to be a huge pain. You have to run a `rake` task to
create the markdown page, edit it in a markdown editor (which never
looked right because of the metadata that it insists on putting at the
top of the file), and then run another rake task to generate the site,
run *another* task to preview it, and then run yet *another* task to
deploy it.

Thing is, I liked the idea of having the blog stored in git and deployed
to GitHub pages\...in theory. But, it got to the point where just the
idea of creating a new post seemed like an awful lot of work.

And then, from time to time, it would fail, either because I checked in
the wrong things, or the ruby versions got screwy, whatever, which meant
that I had to spend a bunch of time tracking down the problem and fixing
it.

The nice thing about Ghost, especially the hosted solution, is that I
don\'t have to worry about any of that. It\'s got a nice interface for
managing the blog, a nice interface for adding new entries, and so on.
True, it\'s not as mature - they\'ve only recently added static pages,
and if you want a menu you have to add it to the theme - but still, when
it comes time to actually write, all I have to do is write. That\'s
nice.

Plus, it\'s written in [Node](http://nodejs.org), so there\'s that. :-)
