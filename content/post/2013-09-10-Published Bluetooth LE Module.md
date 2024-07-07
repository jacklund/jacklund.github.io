---
date: "2013-09-10T00:00:00Z"
tags:
- bluetooth
- nodejs
- iot
title: Published Bluetooth LE Module
---

Well, I published my Node.js module for Bluetooth LE,
[btle.js](https://github.com/jacklund/btle.js) (pronounced \"Beetle
Juice\") to [npm](https://npmjs.org/package/btle.js). Even though it\'s
labeled version 0.1.0, it\'s got most of the functionality that\'s
necessary for Bluetooth LE - reading attributes, writing commands and
requests, and listening for notifications. I\'m hoping to add more
functionality over the next few weeks/months.

The main reason I was doing this was to get my [TI
SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag/index.shtml?DCMP=sensortag&HQS=sensortag-bn)
working with my Raspberry Pi and Bluetooth LE dongle, which it now does.
I\'ve even got the beginnings of a Node.js module for the sensortag,
[sensortag.js](https://github.com/jacklund/sensortag.js), which is built
on top of btle.js. I\'ve got everything working except the Barometric
Pressure sensor readings and the Gyroscope readings.

The nice thing about btle.js is that it\'s purely native C++ code,
talking directly to the Linux Bluetooth stack - it\'s not having to
shell out to run gatttool, for instance, which is pretty nice.

For anyone wanting to write some Bluetooth LE code for Linux in a
non-Node.js environment, I extracted the low-level I/O code from the
[Bluez](http://www.bluez.org/) project, removing the dependency on glib
so that it\'s a true, low-level Linux I/O package. The higher-level code
is dependant on [libuv](https://github.com/joyent/libuv), of course - I
couldn\'t figure out any good way to separate them - but it shouldn\'t
be too difficult to extract what you need from there as well.
