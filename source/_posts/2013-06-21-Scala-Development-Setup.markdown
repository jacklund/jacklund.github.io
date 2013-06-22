---
layout: post
title: Scala Development Setup
date: 2013-06-21 21:56
comments: true
categories: scala eclipse idea sbt maven
---
I had this idea for doing a Scala project integrating WebSockets with [Kafka](http://kafka.apache.org/), so I spent a good bit of today trying out different configurations for doing Scala development. My first pass was to try [IntellijIDEA](http://www.jetbrains.com/idea/features/scala.html) from JetBrains. I really liked their [RubyMine](http://www.jetbrains.com/ruby/) IDE for Ruby, and people were raving about IDEA for Scala development, so I gave it a go.

It's actually not a bad IDE. I had some trouble navigating, but most of that was because I was so used to Eclipse. However, the big thing that was lacking was good [sbt](http://www.scala-sbt.org/) support. Whatever project I was going to use sbt, since that seems to be the builder of choice, and the integration with IDEA seemed to be, well, not really good. You can use sbt to generate the IDEA project, but there didn't seem to be a good way to have IDEA use the dependencies defined in the sbt build file for the code completion, which seemed to me to be a big problem.

So, I tried out using Eclipse (which wasn't a stretch since I use it for my Java work), and [sbteclipse](https://github.com/typesafehub/sbteclipse), which is an sbt plugin for Eclipse. It worked really well! Basically, you install the plugin per the instructions, create a minimal sbt build file for the project, and then type ``sbt eclipse`` to generate the Eclipse configuration. The cool thing is, you can do this at any time, so if you add a dependency to the build file, just do ``sbt eclipse`` again, then refresh the project in Eclipse, and suddenly it has your dependency. Very nice.

The one thing I needed to do, which I eventually found out how to do, was to have sbt use my local maven repository as one of its repositories. sbt uses [ivy](http://ant.apache.org/ivy/) under the covers, so Maven integration is there, it just doesn't know about all the local Maven repositories. to add it, I just had to add the following to my ``~/.sbt/global.sbt`` file:

```
    resolvers += "Local Maven Repository" at "file://"+Path.userHome.absolutePath+"/.m2/repository"
```

and, voila, it worked!