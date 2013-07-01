---
layout: post
title: Got Them Bluetooth LE Bluez
date: 2013-07-01 17:15
comments: true
categories: bluetooth_le python bluez mqtt gatt
---
I'm trying to get my Raspberry Pi to talk to my [TI Sensortag](http://www.ti.com/ww/en/wireless_connectivity/sensortag/index.shtml?INTC=SensorTag&HQS=sensortag) over Bluetooth LE, using an [IOGear Bluetooth 4.0 USB adapter](http://www.iogear.com/product/GBU521/). I chose that adapter because it is supported by Linux, and so far it works quite nicely with the Pi.

To start with, I'd like to write something to pull the sensor data - temperature, humidity, pressure, etc - from the SensorTag and publish it over [MQTT](http://mqtt.org/). However, reading the data seems to be problematic.

Problem #1 is that the most-used bluetooth package for Python, [PyBluez](https://code.google.com/p/pybluez/) had their last release (0.18) in November, 2009, which, obviously, doesn't include Bluetooth LE. Not a problem, I thought, I'll just fork the project, add the bindings for LE, and off we go!

Which brings me to problem #2: the developers of the [BlueZ](http://www.bluez.org/) protocol stack, which is how you access bluetooth from, well, pretty much everywhere, haven't seen fit to include [GATT](http://en.wikipedia.org/wiki/Bluetooth_profile#Generic_Attribute_Profile_.28GATT.29) support either via a public library or via D-Bus, despite the fact that they've had the code in their codebase since around 2010.

\****facepalm***\*

Look, I understand that everybody's busy and all, but, really, you couldn't, in the last THREE YEARS break out the GATT code into a library so that people can access it? Really?

Of course, I shouldn't complain, I guess - they do provide a nice little command-line tool, ``gatttool``, to let you do GATT stuff, just nothing to do, you know, programming with. Somebody (who is much braver than I) is even trying to [access that via Python and pexpect](https://github.com/msaunby/ble-sensor-pi), which works, sort of, but, uh, really, there has to be a better way.

So, I guess my only option is to pull the GATT code out of the BlueZ project and add it to the Python project. It's ugly, but it's better than expect scripts.

And, really, BlueZ developers: Come on, throw us a bone here.