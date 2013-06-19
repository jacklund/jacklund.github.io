---
date: 2013-05-07 17:00
title: Falling in Love with Git
categories: git
layout: post
comments: true
---
As I mentioned earlier, we're transitioning from subversion to git, and one of the things we need to do is convert from many-projects-per-repository, which is what we had in subversion, to one-project-per-repository for git. This is partly to make things nicer, and partly because it will make [Jenkins](http://jenkins-ci.org/) work better. So, thinking in subversion-terms, I thought I'd have to do it myself, and even tried by renaming all the files in one of the projects into the upper-level directory, removing all the other projects, committing locally, and then pushing to a new repo. Royal pain.

And then, I came across [this](https://help.github.com/articles/splitting-a-subpath-out-into-a-new-repository). Oh. My. God. Git actually does the work for you. It's so easy, you just do the ``git filter-branch`` command, and suddenly your project is its own repository! Then, you just change the remote repository (``git remote rm origin`` followed by ``git remote add`` _new-url_), and Bob's your uncle, you have a new, smaller repository.

I mean, seriously? That is AWESOME!

So, to be explicit, here's the series of commands:

```bash
    $ git clone repo-url
    $ cd repo
    $ git filter-branch --prune-empty --subdirectory-filter subdir-name master
    $ git remote rm origin
    $ git remote add new-repo-url
    $ git push -u origin --all --tags
```

**Updated 05/12/2013**
Okay, after messing around with this for some time, I've discovered that the above doesn't work quite right, because the pathnames in the commits are wrong - instead of being _subdir-name_``/src/foo/Bar.java``, it ends up being ``src/foo/Bar.java``, which doesn't work. So, instead of trying to pull each subdirectory into their own repository, I'm taking the existing repository, with a bunch of subdirs, including the ones I'm interested in, and simply using ``git filter-branch`` to get rid of the ones I'm not interested in, as well as update the tags. So, here's what I've got now, which seems to work well:
<script src="https://gist.github.com/jacklund/5565462.js"></script>
