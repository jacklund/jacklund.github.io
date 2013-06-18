---
date: 2012-04-03
title: Deployment Sucks
layout: post
comments: true
---
We're using Ant + Ivy + Jenkins + Artifactory to do our builds - Ant to build, Ivy for dependency management, Jenkins for continuous integration, and Artifactory to store the build artifacts. One thing we're struggling with is how to promote an artifact through the three stages - build -> stage -> release. Jenkins puts it into the build repository once it passes CI, so that part is easy. However, having an automated process for moving the artifacts from build to stage and from stage to release is painful, at best, which is surprising.

The problem is with Ivy. It's a pretty good tool for dependency management - it finds and downloads specific versions of jars for you, which is nice, but it's not so good at going outside that realm, like, for instance, pulling down an artifact from the repository, and then pushing it back again with updated metadata, into a different repository.

We've tried just specifying the artifact as the depndency, giving it a revision of "latest.integration" to pull it from build, and then publishing to stage, for instance, but Ivy complains that it can't be its own dependency, which makes sense. We've tried publishing it as a different module name to stage, for instance, but then you run into the same problem getting it from stage to release.

There has to be a better way. Unfortunately, the Artifactory people aren't much help - their solutions involve buying their "pro" version, and using their REST interface. Blech.
