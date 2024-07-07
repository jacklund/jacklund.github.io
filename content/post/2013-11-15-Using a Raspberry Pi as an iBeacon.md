---
date: "2013-11-15T00:00:00Z"
tags:
- iot
- raspberry pi
title: Using a Raspberry Pi as an iBeacon
---

There\'s an excellent [blog post by by James Nebeker and David G.
Young](http://developer.radiusnetworks.com/2013/10/09/how-to-make-an-ibeacon-out-of-a-raspberry-pi.html)
about simulating an [iBeacon](http://en.wikipedia.org/wiki/IBeacon)
using a Raspberry Pi and a bluetooth dongle. Since I already had both, I
thought I\'d give it a try. It worked really well, and I\'ve even put
together the files necessary to do it [in my GitHub
repository](https://github.com/jacklund/piBeacon).

You\'ll need to install [BlueZ](http://www.bluez.org/) as well for this
to work. Once you get it all installed and working you can use one of
the mobile iBeacon apps, such as [iBeacon
Locate](https://itunes.apple.com/us/app/ibeacon-locate/id738709014?ls=1&mt=8).
Very fun to play with, and a good way to be able to develop mobile apps
to access iBeacons without having to wait for them to come out.

Also, it\'s just fun to play with.
