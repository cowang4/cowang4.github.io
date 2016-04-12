---
layout: post
title: Physical Computer Key
---


### U2F

I recently ran across a 2-factor authentification protocol called [U2F](https://en.wikipedia.org/wiki/Universal_2nd_Factor). It was developed by Yubico and Google and manifests itself as a physical usb 'key'
that communicates its identity using [Public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography). My key was $18, but I bought two so $36 (I'll explain why), has no battery, fits on my keychain,
and the spec and source code are online. [Check it out.](https://www.yubico.com/products/yubikey-hardware/)
U2F can be used by websites that implement
the protocol as long as you're running Google Chrome. The current websites at the time of
writing that support U2F as a mode of 2-factor authentification include,
Google, Dropbox, Github, and more. I also use it as a 2nd auth for my OS. I use
Ubuntu 15.10, so that is what the following guide assumes you want to do as
well.

![]({{ site.baseurl }}/images/U2F.jpg)

### On the Web

Once you get your U2F key(s), to use them on the web, all you need to do is add
a udev rule for them. This should do the trick:

~~~
sudo wget -O /etc/udev/rules.d/70-u2f.rules https://raw.githubusercontent.com/Yubico/libu2f-host/master/70-u2f.rules
~~~~~~~~

Now you can either restart udev or restart your computer.

Now plug the key in and follow the compatible website's instructions to set it
up.:boom:

### On the Desktop

Setting up U2F on the desktop is a teeny bit more complicated than for web,
especially because Ubuntu 15.10 doesn't have an official `apt` package yet.
:frowning: _Disclaimer: You can permantently lock your OS if you f this up._
Backups are always a good idea. Don't worry though, I didn't break anything, so read on.

Download the sources from Github:

~~~
git clone git://github.com/Yubico/pam-u2f.git
~~~

Install some dependencies:

~~~
apt-get install autoconf automake libtool libpam-dev asciidoc xsltproc libxml2-utils docbook-xml --no-install-recommends
~~~

Run these build commands:

~~~
autoreconf --install
./configure --with-pam-dir=/etc/x86_64-linux-gnu/security/
make check
make install
~~~

This should have built the sources and copied a `pam_u2f.so` file to that
`/lib/x86_64-linux-gnu/security/` directory.

Next we are going to add a line to `/etc/pam.d/common-auth` that allows either the U2F key
or your password to unlock the computer. This way you can test to make sure
your key(s) work before locking yourself out of your computer. Add this:

~~~
auth sufficient pam_u2f.so debug cue
~~~

Time to associate the key with your username. Make a file
`~/.yubico/u2f_keys`. Run `pamu2fcfg -umyusername` where `myusername` is your
account's username. You'll have to press the magic button on the key and the
utility should print out something like:

~~~
myusername:oirwuriuvnwboom(Lots of stuff)djlssflj,sfhsfksfkhHexNumber
~~~

In the format:

~~~
<username>:<KeyHandle>,<UserKey>
~~~

If you have multiple keys, run `pamu2fcfg` for each one and add as such:

~~~
<username>:<KeyHandle1>,<UserKey1>:<KeyHandle2>,<UserKey2>
~~~

Now reboot your computer, :pray:, and test if the key works by first plugging it in,
then typing your password, then pressing the capacitive
button on the key when it flashes. If it doesn't work or doesn't prompt for the key, __do not continue__ or you'll lock yourself out of your computer. If they key doesn't work you're on your own to fix the installation (jk send me an
email). If it does work, you can change the `sufficient` in the line we wrote
in `/etc/pam.d/common-auth` to `required`. PSA: this can be dangerous, backup first and proceed at
your own risk.

Now, after you type your password, it will be wrong if no key is plugged in.
The login should cue you with 'Please touch the device.' when you login and the
light on the key should flash. Press the button and you're all set. :clap:

Oh yeah, and it works for `sudo` as well. Try it out.

### Why two?

Because I don't trust myself not to lose the one. I suggest buying a second
key, registering and testing it, and storing it in a safe place, like an actual
safe. You shouldn't have to ever use the backup key again.

### Security

Yes, I know my computer is still vunerable. 2-factor authentification doesn't
make your accounts perfectly safe. Especially if your hard drive is
unencrypted. The point of the key in my mind is 70% fun and 30% security, but
you may feel differently.

### Sources

[Ubuntu 14.04 Guide](https://seabre.github.io/blog/2015/10/17/local-two-factor-authentication-with-u2f-on-ubuntu-14-dot-04/)

[Yubico Install Instructions](https://developers.yubico.com/pam-u2f/)
