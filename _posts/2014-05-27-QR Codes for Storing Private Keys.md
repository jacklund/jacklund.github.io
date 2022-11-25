---
layout: single
title:  "QR Codes for Storing Private Keys"
---

So, storage of private keys is a problem.

Just storing it on your computer isn\'t such a big problem - hopefully,
you\'re storing it encrypted using software whose encryption you trust.

However, backing them up - that\'s another issue. Do you trust storing
them in the cloud? I don\'t. Do you keep them on a backup disk or USB
dongle? What if they fail?

After looking over the options, I found [this
post](http://security.stackexchange.com/a/51776) at the ever-excellent
StackExchange. Basically, the idea is that you convert the ASCII
exported private key to a QR code, print that, note the key ID on the
printed copy, and then delete (securely) the exported private key. You
then keep the printed QR code somewhere safe - a safe in your house or
in a safe deposit box. Then, you can use a QR scanner to convert it back
to ASCII (it\'s still secure because you\'ll need your passphrase to use
the key).

In the post, they recommend using an online QR scanner that uses
javascript so it all happens in the browser. Since I\'m much more
paranoid than that, I just use
[qrencode](http://fukuchi.org/works/qrencode/manual/) on my MacBook,
like so:

Install `qrencode`:

    $ brew install qrencode

Export the secret key and covert it to a QR code:

    $ gpg --export-secret-key -a | qrencode -l L -8 -o key.png

Then you just print the QR code and then securely delete it.
