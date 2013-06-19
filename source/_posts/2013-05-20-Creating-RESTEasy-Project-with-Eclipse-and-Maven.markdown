---
date: 2013-05-20  
title: Creating RESTEasy Project with Eclipse and Maven
categories: resteasy eclipse maven java
layout: post
comments: true
---
Handy safety tip: to create a RESTEasy-friendly Maven project in Eclipse, do the following:

1. Create the project as a simple Maven project
2. In the project Properties -> Project Facets, make the project faceted, and select "Dynamic Web Module" (you may get a NullPointerException, but it doesn't seem to matter). Under "Further Configuration...", point the WebApp directory to be ``src/main/webapp``, and have it create the ``web.xml`` file.
3. Under "Web Content Settings", set the Context root
4. Under "Deployment Assembly", select "Add...", select "Java Build Path Entries", and select "Maven Dependencies". It should put them in ``WEB-INF/lib``.
5. Add the code and the dependencies to the ``pom.xml``. Make sure to add the RESTEasy stuff to the ``web.xml`` file.

You should be able to deploy it from Eclipse into Tomcat without any issues
