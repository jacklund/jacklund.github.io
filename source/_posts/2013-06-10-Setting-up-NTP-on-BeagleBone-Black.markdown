---
date: 2013-06-10 11:50  
title: Setting up NTP on BeagleBone Black 
categories: beaglebone linux
layout: post
comments: true
---
For some reason, they didn't seem to see the need to set up the time service on the BeagleBone Blacks by default. I tried using the instructions from [this gentleman](http://derekmolloy.ie/automatically-setting-the-beaglebone-black-time-using-ntp/), but I had to make some modifications.

1. Install NTP
 
```bash
        $ opkg update && opkg install ntp
```

2. Edit ``/etc/ntp.conf``, adding ``pool.ntp.org`` as the server:

```apache
        # This is the most basic ntp configuration file
        # The driftfile must remain in a place specific to this
        # machine - it records the machine specific clock error
        driftfile /etc/ntp.drift
        # This obtains a random server which will be close
        # (in IP terms) to the machine.  Add other servers
        # as required, or change this.
        server pool.ntp.org
        # Using local hardware clock as fallback
        # Disable this when using ntpd -q -g -x as ntpdate or it will sync to itself
        server 127.127.1.0
        fudge 127.127.1.0 stratum 14
        # Defining a default security setting
        restrict default
```

3. Set your local time:

```bash
        $ cd /etc
        $ rm -f localtime
        $ ln -s ../usr/share/zoneinfo/America/Chicago localtime
```

4. Edit ``/etc/default/ntpdate``:

```apache
        # Configuration script used by ntpdate-sync script

        NTPSERVERS="pool.ntp.org"

        # Set to "yes" to write time to hardware clock on success
        UPDATE_HWCLOCK="yes"
```

5. Now, reboot and see if the clock gets set correctly
