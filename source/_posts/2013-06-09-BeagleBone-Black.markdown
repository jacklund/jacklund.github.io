---
date: 2013-06-09 20:00  
title: BeagleBone Black  
categories: beaglebone linux
layout: post
comments: true
---
Got my two [BeagleBone Blacks](http://beagleboard.org/Products/BeagleBone%20Black) from [AdaFruit](http://adafruit.com/) on Friday. Really nice. I bought them without enclosures because, well, the enclosures were $20, and the BeagleBones themselves were only $45. The nice thing is, though, that they fit exactly into an Altoids tin, so I turned two into my enclosures. I used tin snips to cut holes for power and Ethernet, and lined them with electrical tape to insulate the board from the box. Here's what they ended up looking like:

![](https://dl.dropbox.com/s/bpgc7lc5azhazw5/altoids_closed.jpg "Closed")

![](https://dl.dropbox.com/s/d87y38ydxqtcv2q/altoids_open.jpg "Open")

![](https://dl.dropbox.com/s/7nocbp8nnb65pwn/altoids_working.jpg "Running")

By default, they run [Ångström Linux](http://www.angstrom-distribution.org/). The default programming environment for them is a variant of Javascript they call ["Bonescript"](http://beagleboard.org/Support/BoneScript), which runs via [Node.js](http://nodejs.org/). However, a kind soul had put together a Python library called [PyBBIO](https://github.com/alexanderhiam/PyBBIO), which I downloaded and tried to install. I kept getting the following warning, however:

    Warning: you seem to have a BeagleBone image which only has drivers for the PWM1 module, PWM2A and PWM2B will not be available in PyBBIO.
    You should consider updating Angstrom!

I tried updating the OS using [these instructions](http://learn.adafruit.com/beaglebone-black-installing-operating-systems/overview), however it didn't make the problem go away. That's when I discovered that PyBBIO [doesn't support the 3.8 kernel that comes with BB Blacks](https://github.com/alexanderhiam/PyBBIO/issues/18), so it looks like I'm stuck with Javascript for the moment.
