---
layout: post
title: "Eclipse with Subversion 1.8 on OS X"
date: 2013-06-18 14:14
comments: true
categories: subversion eclipse osx
---
I wasted a considerable amount of time following the instructions from [this page](http://subclipse.tigris.org/wiki/JavaHL) on how to install Subclipse on OS X, trying to get MacPorts to install the right version of Subversion (Why subversion, you may ask? Because a customer is still using it, that's why...).

It turns out that MacPorts doesn't even have the 1.8 version of Subversion - it's only got 1.7, which won't work with the version of Subclipse I'm using. So, I had to go to [WanDisco](http://www.wandisco.com/subversion/download#osx) and download it (after removing the MacPorts versions first), and then everything worked.