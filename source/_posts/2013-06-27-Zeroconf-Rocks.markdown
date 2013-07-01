---
layout: post
title: Zeroconf Rocks!
date: 2013-06-27 22:27
comments: true
categories: zeroconf bonjour linux raspberry-pi beaglebone-black
---
I have a lot of computers on my network now - my laptop and desktop, my wife's 3(!) computers, her iPad, two Linux servers and miscellaneous other devices, along with my Raspberry Pi and two BeagleBone Blacks, so managing all these was starting to be a pain. So, just on a hunch, I wanted to see if [Bonjour/Zeroconf/Avahi](http://en.wikipedia.org/wiki/Zero-configuration_networking) could help. I did some investigation, and it turns out that you can access any machines that advertise on Zeroconf by _name_.local. Since I work predominantly on Macs, I was already pretty comfortable with the idea of dynamically finding service. When I brought up my Bonjour Browser application, however, I was surprised: My two BeagleBone Blacks were already advertising (the wrong IP address for my network, but still)!

So, first step: set up the Raspberry Pi to do the same. Fortunately, the Internet came to the rescue, in the form of [this page](http://elinux.org/RPi_Advanced_Setup) about setting up Zeroconf on the Pi. I just followed the instructions, and it worked.

Next, I had to fix my BeagleBone Blacks. To do this, I found the DHCP daemon config file, ``/etc/udhcpd.conf``, which looked like this:

    start      192.168.7.1
    end        192.168.7.1
    interface  usb0
    max_leases 1
    option subnet 255.255.255.252

and which I changed to match my network:

    start      192.168.1.2
    end        192.168.1.99
    interface  usb0
    max_leases 1
    option subnet 255.255.255.0

Then, I just restarted them, and they suddenly had the right IP addresses.

Now, all I have to do to SSH into my Pi is do ``ssh raspberry-pi.local``. For my BeagleBones, it would be ``ssh beaglebone-red.local`` or ``ssh beaglebone-blue.local``.

W00t!!!!
