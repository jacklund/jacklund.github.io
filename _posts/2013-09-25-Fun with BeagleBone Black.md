---
layout: single
title:  "Fun with BeagleBone Black"
---

So, I\'ve decided to try to play with
[ZigBee](http://en.wikipedia.org/wiki/ZigBee), and since I have a couple
of BeagleBone Blacks hanging around doing nothing, I thought I\'d try
setting it up on them.

First thing I came across was that the BBB\'s seem to have issues with
[accessing their
UARTS](https://groups.google.com/forum/#!topic/beagleboard/4e2T6XH-fNM).
Even via bonescript, there seem to [be
issues](http://stackoverflow.com/questions/17497060/node-js-script-not-setting-beaglebone-black-mux).

So, first thing I did was to upgrade to the latest firmware, and then do
`opkg update` followed by `opkg upgrade` to get all the latest stuff.
However, when I tried to run a bonescript program (the one from
[here](http://stackoverflow.com/questions/17497060/node-js-script-not-setting-beaglebone-black-mux)),
I got `module bonescript not found`!!!. WTF?

Since I had seen in the [bonescript
docs](http://beagleboard.org/Support/BoneScript/pinMode/) that the
`pinmode` call doesn\'t really work until bonescript 0.2.3, I figured
I\'d be smart and just try to upgrade to the latest npm version of
bonescript.

Long story short: bad idea. I had to go through all sorts of hell to
make the npm install work (including editing the node-gyp configuration
file to avoid a bug in the python version check), I finally got
bonescript 0.2.3 working. So, I tried my test program and\...the network
connection died. Every time I ran the program, the same thing happened.

Turns out, this is an [issue with the latest
bonescript](https://github.com/jadonk/bonescript/issues/51). So, I ended
up having to back down to the previous version of bonescript via opkg.
Once I investigated the `module bonescript not found` issue, it turned
out that, for some reason, the bonescript module in
`/usr/lib/node_modules` wasn\'t getting written, so I had to do a
`opkg remove bonescript` followed by `opkg install bonescript` to make
it all work.

All this, and I haven\'t even tried to get the XBee stuff working yet.
Oy.
