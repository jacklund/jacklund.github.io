---
layout: single
title:  "More Fun With BeagleBone Black"
---

I\'ve discovered that owning a BeagleBone Black (or, in my case, two) is
kind of like owning one of those really, really nice\
cars that you buy but you can almost never drive because it spends so
much time in the shop. It\'s a lovely piece of hardware, it\
really is, but the people who created it seem to have gone to great
lengths to make sure that all you can do is sit back and admire\
it, because you certainly won\'t be able to do anything useful with it.

My next foray into BBB craziness was attempting to get a wifi dongle to
work with it, so I don\'t have to constantly hook it up\
to my wireless adapter. Should be easy, right? Of course not, this is
the BeagleBone Black we\'re talking about. *Nothing* is easy.

I bought the [Edimax
EW-7811-UN](http://www.edimax.com/en/produce_detail.php?pd_id=347&pl1_id=1),
which is recommended for BeagleBones,\
Raspberry Pi\'s, and the BBB. Unfortunately, what I didn\'t realize is
that, while it *used* to work with the BBB, some OS changes were\
made to it so that it takes a lot of wrangling to get it to work, if you
can get it to work at all (I couldn\'t).

First of all, it seems that, at some point, the drivers weren\'t really
available, so you would have to build them yourselves. I think\
the drivers are available now, but I went ahead and compiled the drivers
per\
[these
instructions](http://www.codealpha.net/864/how-to-set-up-a-rtl8192cu-on-the-beaglebone-black-bbb/).\
The next hurdle was getting it to work with
[connman](https://connman.net/), the connection manager, which is,
basically,\
almost completely undocumented - Fun! (***Note to developers
everywhere:*** in the words of an former boss - \"if it ain\'t
documented,\
it doesn\'t exist\").

However, from what I can gather, `connman` uses
[wpa\_supplicant](http://hostap.epitest.fi/wpa_supplicant/) for the WPA
transactions\
(if you\'re using WPA, which, um, you should be). So, after fiddling
around for a while, here\'s what my `/var/lib/connman/settings`\
file looks like:

    [global]
    OfflineMode=false

    [Wired]
    Enable=true

    [WiFi]
    Enable=true

And here\'s my `/var/lib/connman/wifi.config` file:

    [service_home]
    Type=wifi
    Name=my_ssid
    Security = wpa2-psk
    Passphrase=my_encrypted_passphrase

where `my_ssid` is my SSID name, and `my_encrypted_passphrase` is the
result of running `wpa_passphrase <ssid> <password>`.

Finally, my `/etc/wpa_supplicant.conf` - I\'m running WPA2-Personal on
my wireless router, so this is, as far as I can tell, the\
way you set up `wpa_supplicant`:

    ctrl_interface=/var/run/wpa_supplicant
    ctrl_interface_group=0
    update_config=1

    network={
            ssid="my_ssid"
            #psk=my_encrypted_passphrase
            psk="my_unencrypted_passphrase"
            key_mgmt=WPA-PSK
            proto=RSN
            pairwise=CCMP TKIP
            group=CCMP TKIP
    }

Now, just getting to this point took a lot of tweaking and messing
around with various settings. However, it\'s still not connecting.\
It\'s finding the access point, and even getting its MAC address, but
it\'s unable to authenticate. Here\'s what happens when I run\
`wpa_supplicant` manually in debug mode:

    # wpa_supplicant -iwlan0 -c/etc/wpa_supplicant.conf -d
      (irrelevant parts omitted)
    wlan0: New scan results available
    wlan0: Selecting BSS from priority group 0
    wlan0: 0: 5c:96:xx:xx:xx:83 ssid='my_ssid' wpa_ie_len=0 rsn_ie_len=20 caps=0x11 level=87
    wlan0:    selected based on RSN IE
    wlan0:    selected BSS 5c:96:xx:xx:xx:83 ssid='my_ssid'
    wlan0: Request association: reassociate: 0  selected: 5c:96:xx:xx:xx:83  bssid: 00:00:00:00:00:00  pending: 00:00:00:00:00:00  wpa_state: SCANNING
    wlan0: Trying to associate with 5c:96:xx:xx:xx:83 (SSID='my_ssid' freq=2462 MHz)
    wlan0: Cancelling scan request
    wlan0: WPA: clearing own WPA/RSN IE
    wlan0: Automatic auth_alg selection: 0x1
    RSN: PMKSA cache search - network_ctx=(nil) try_opportunistic=0
    RSN: Search for BSSID 5c:96:xx:xx:xx:83
    RSN: No PMKSA cache entry found
    wlan0: RSN: using IEEE 802.11i/D9.0
    wlan0: WPA: Selected cipher suites: group 16 pairwise 16 key_mgmt 2 proto 2
    wlan0: WPA: clearing AP WPA IE
    WPA: set AP RSN IE - hexdump(len=22): 30 14 01 00 00 0f ac 04 01 00 00 0f ac 04 01 00 00 0f ac 02 00 00
    wlan0: WPA: using GTK CCMP
    wlan0: WPA: using PTK CCMP
    wlan0: WPA: using KEY_MGMT WPA-PSK
    WPA: Set own WPA IE default - hexdump(len=22): 30 14 01 00 00 0f ac 04 01 00 00 0f ac 04 01 00 00 0f ac 02 00 00
    wlan0: No keys have been configured - skip key clearing
    wlan0: State: SCANNING -> ASSOCIATING
    wpa_driver_wext_set_operstate: operstate 0->0 (DORMANT)
    netlink: Operstate: linkmode=-1, operstate=5
    Limit connection to BSSID 5c:96:xx:xx:xx:83 freq=2462 MHz based on scan results (bssid_set=0)
    wpa_driver_wext_associate
    wpa_driver_wext_set_drop_unencrypted
    wpa_driver_wext_set_psk
    wlan0: Setting authentication timeout: 10 sec 0 usec
    EAPOL: External notification - EAP success=0
    EAPOL: Supplicant port status: Unauthorized
    EAPOL: External notification - EAP fail=0
    EAPOL: Supplicant port status: Unauthorized
    EAPOL: External notification - portControl=Auto
    EAPOL: Supplicant port status: Unauthorized
    RSN: Ignored PMKID candidate without preauth flag

and basically it cycles through this over and over. Now, I\'m sure this
all means something to whoever wrote this code, but to me\
there\'s absoutely nothing helpful here. it\'s written for someone to
try to debug the program, not to help someone troubleshoot\
a problem with their setup. And, yet, this is, as far as I can tell, the
only troubleshooting tool these fine folks have seen\
fit to provide. Nice.

So, after wading through all this garbage, and trying a bunch of
different things, I finally gleaned (with absolutely no help\
from whoever wrote this crappy software, thank you very much!) that
it\'s failing authentication. Why? Who knows. Really, there\'s\
a special circle of hell for people who write error messages like this
for their users to have to decode. When you get there,\
you spend eternity debugging Windows NT BSOD error messages.

So, I tried looking up the incredibly clear message
`EAPOL: Supplicant port status: Unauthorized`. Now, there are a number
of posts that\
imply that you need to have `eth0` disconnected in order for this to
work (which makes absolutely no sense to me whatsover -\
since when are you only able to connect to one network interface at a
time?), but doing that seems to make no difference. I\'ve also\
seen a lot of descriptions of procedures people use to get this to work
that are the technological equivalent of holding a dead\
chicken over the machine and praying. This didn\'t work for me either
(well, okay, I didn\'t try the chicken\...yet).

Which brings up my gripe: why is this so \$@%\^!&@ hard? Why do you have
to go through this hell to just connect to a wireless access\
point - something that they do in the Mac and Windows worlds all the
time? Would it be so bleeping hard to just spit out some relatively\
helpful messages - something along the lines of \"authentication
failed\", or even \"bad passphrase\"? Something.

If you are guilty of writing software like this, do me a favor: go to
the mirror, grab yourself by the collar and slap yourself\
repeatedly, saying \"Bad Programmer! Bad!\" over and over, because it\'s
exactly what I\'d be doing if I were there with you.
