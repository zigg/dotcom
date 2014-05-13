# Sandboxing Homebrew

- date: 2014-04-22 02:14
- tags: homebrew, osx, sudo

----

I've been at this Unix thing for… many years.  Let's just leave it at
that.  In this time I've developed a healthy respect for the wall the
operating system typically erects that makes this dichotomy:

-   Whatever's in `$HOME` is all yours; you have rights to it, you
    can blow it away

-   Whatever's in `/usr` is everyone's; you can only muck about
    with it if you use `su` or [Sudo](http://sudo.ws/), and filesystem
    permissions will stop you if you try to do your mucking without
    them

[Homebrew](http://brew.sh/) has a different M.O. in its typical use
case.  Your Mac is your own, it presumes, so it gives `/usr/local` to
your user account, then runs all the builds and installs under your
user account.  I'm very used to running builds in my own home
directory and then using Sudo to install them, but not having any
wall is new to me.  I've tripped over it more times than I can
count—mostly by using a platform's installer, such as
[Python](https://www.python.org/)'s [pip](http://www.pip-installer.org/),
without setting the appropriate options to tell it to install to
my work area instead of `/usr/local`.

So I thought I'd take a compromise path with Homebrew: I'd give it
a user-account-based sandbox—chosen instead of `root` to keep
Homebrew's nice behavior of not requiring `root` and thus risking
the screwup of the rest of my system—then rig up some Sudo magic
to make it relatively easy to still use `brew` to do management of
the Homebrew area.  This approach also keeps Homebrew (or, more
accurately, the packages it builds and installs) out of my home
directory.

Here's what I did, but **WARNING: this is a work in progress.  Don't
do this blindly, or you could find yourself in deep doodoo!**
Especially right before you need to do real work.  Play with it [in
a VM](http://ntk.me/2012/09/07/os-x-on-os-x/) if you need to.

## Create the user account

Creating a system account on OS X—typically used to sandbox
daemons—that's not a regular user account is only slightly voodoo.
I used [`add_system_user.sh`](https://serverfault.com/a/532860/115137),
which I've reproduced here:

````bash
#!/bin/bash

if (( $(id -u) )) ; then
    echo "This script needs to run as root"
    exit 1
fi

if [[ -z "$1" ]] ; then
    echo "Usage: $(basename $0) [username] [realname (optional)]"
    exit 1
fi

username=$1
realname="${2:-$username}"

echo "Adding daemon user $username with real name \"$realname\""

for (( uid = 500;; --uid )) ; do
    if ! id -u $uid &>/dev/null; then
        if ! dscl /Local/Default -ls Groups gid | grep -q [^0-9]$uid\$ ; then
          dscl /Local/Default -create Groups/_$username
          dscl /Local/Default -create Groups/_$username Password \*
          dscl /Local/Default -create Groups/_$username PrimaryGroupID $uid
          dscl /Local/Default -create Groups/_$username RealName "$realname"
          dscl /Local/Default -create Groups/_$username RecordName _$username $username

          dscl /Local/Default -create Users/_$username
          dscl /Local/Default -create Users/_$username NFSHomeDirectory /var/empty
          dscl /Local/Default -create Users/_$username Password \*
          dscl /Local/Default -create Users/_$username PrimaryGroupID $uid
          dscl /Local/Default -create Users/_$username RealName "$realname"
          dscl /Local/Default -create Users/_$username RecordName _$username $username
          dscl /Local/Default -create Users/_$username UniqueID $uid
          dscl /Local/Default -create Users/_$username UserShell /usr/bin/false

          dscl /Local/Default -delete /Users/_$username AuthenticationAuthority
          dscl /Local/Default -delete /Users/_$username PasswordPolicyOptions
          break
        fi
    fi
done

echo -e "Created system user $username (uid/gid $uid):\n"

dscl /Local/Default -read Users/_$username

echo -e "\nYou can undo the creation of this user by issuing the following commands:\n"
echo "sudo dscl /Local/Default -delete Users/_$username"
echo "sudo dscl /Local/Default -delete Groups/_$username"
````

Armed with this script: I did

````
$ ./add_system_user.sh homebrew
````

and now my user account is ready.

## Set up Homebrew

Next, I prepared `/usr/local` for use with Homebrew:

````
$ sudo -i
# mkdir /usr/local
# chown _homebrew /usr/local
# chmod 0755 /usr/local
# cd /usr/local
# sudo -u _homebrew git clone https://github.com/Homebrew/homebrew.git /usr/local
````

Because our new `_homebrew` user won't have a home directory and thus
no place to stash Homebrew logs, we'll go ahead and treat it like a
system account and give it its own log directory too:

````
# mkdir /var/log/homebrew
# chown _homebrew /var/log/homebrew
````

## Set up Sudo

It is currently shockingly inconvenient to actually invoke `brew`.  But
this is easy to repair, with a little Sudo magic.

First, an extra stanza for `sudoers`, which can be added via `visudo`:

````
# Homebrew
%admin	ALL=(_homebrew) SETENV: /usr/local/bin/brew
````

The `SETENV` is necessary because we need to tell `brew` about the log
directory we gave it.  The final touch, added to the end of our
`$HOME/.profile`:

````bash
alias brew='sudo -u _homebrew HOMEBREW_LOGS=/var/log/homebrew /usr/local/bin/brew'
````

**Update:** The above works for the formulae I've tested so far, but
not `node`.  This variant solves that problem by creating temporary
home directories for each run.  It's probably best converted into a
wrapper script, but for now, this'll do.

````bash
alias brew='tmphome=`sudo -u _homebrew mktemp -d /tmp/_homebrew.XXXXXX`;sudo -u _homebrew HOME=${tmphome} HOMEBREW_LOGS=/var/log/homebrew /usr/local/bin/brew'
````

Now we can `brew` anything just as before, except that Sudo is now
in the picture, switching contexts for us for all Homebrew actions:

````
$ brew doctor
Password:
Your system is ready to brew.
````

## Results

This works really well, actually! The `node` formula wasn't completing
its install, I think because it was trying to lock in *my* home
directory.  I patched that up with the new `brew` alias.  But the
20-odd other formulae I use are working great.

I'm definitely interested in developing this idea further, when I
get some time.  In a virtual machine, like I should have done the
first time.

