# A week of code

- date: 2014-05-24 15:50
- tags: ruby, go, brewdo, osx, packer, vagrant

----

This post is going to be a bit lighter on the hard content than my
usual, but I had a great week of code and I feel like writing about
it.

Developments with [brewdo](/code/brewdo/) led it all off.  Last
Saturday, I finally added install code, and started down the path of
configuring the user's [Sudo](http://www.sudo.ws/) configuration for
them—something that took me awhile because I didn't want to screw
it up.  I came up with a solution I was finally comfortable with
and merged it, but when I studied the result I decided that brewdo
in its current form had crossed that line where bash is the wrong
language to do it in.  So I decided to finally write my first Ruby
program by porting brewdo.

I'd been looking at Ruby on and off for some months now, mostly
because it's very difficult to get away from.  It's everywhere today,
much like [Perl](http://www.perl.org/) once was—and I'm not even doing
[Rails](http://rubyonrails.org/).
[zigg_ebooks](https://twitter.com/zigg_ebooks) runs on it, by way of
the excellent [twitter_ebooks](https://github.com/mispy/twitter_ebooks)
(though I'm probably running the only installation on
[OpenBSD](http://www.openbsd.org/).  I'd read Ruby code and even
tried to contribute a few small things here and there, but this was
my first swing at doing it for real.

I think it went fairly well, but it was just a bit of a frustrating
experience, even when dealing with just the readily-available
standard library (my goal was to have one self-contained script)
and not digging into the peripheral toolset.  The documentation I
found was thin, and more than a few times I could see how to do
something, but I found myself wishing there was a better way.  But
maybe that's in part because it was my first hit; with time, maybe
I'll appreciate it more.  Regardless, the end result was definitely
an improvement over the bash version, and the investigation quite
stimulating.

As I was working through testing the various incarnations of brewdo,
something else really bothered me–my virtual machine workflow for
testing.  [Running OS X in
VirtualBox](http://ntk.me/2012/09/07/os-x-on-os-x/) is a solved
problem, but it's shockingly inconvienient compared to the workflow
I'd become used to [using Vagrant with
octothorpe](https://github.com/zigg/octothorpe/blob/master/Vagrantfile).
Buying VMware and the Vagrant VMware plugin was an option, but I
decided instead to go looking to see if I could get this all working
with VirtualBox instead.

This led me down another path that resulted in me finally having my
excuse to look at [Go](http://golang.org/).  People have been
[taking stabs at](https://gist.github.com/timsutton/9195541) the
OS-X-on-VirtualBox Vagrant problem for a little while now, but the end
result was compromised by [Packer](http://www.packer.io/)—the tool of
choice for building Vagrant boxes—not supporting mounting the OS X
install image on a virtual SATA connection instead of IDE.  So I went
in and, over the course of several hours (it takes a long time to
re-run the OS X installation!), [implemented this feature for
Packer](https://github.com/mitchellh/packer/pull/1200).

Doing this, of course, meant learning a little Go.  In contrast to
Ruby, Go immediately made sense to me.  I was initially a bit weirded
out by its build system (particularly the bit about automatically
fetching code from repositories all over the place), but the more
I ruminate on it, the more I'm intrigued.  Now I want to have a Go
project, but it's probably silly to rewrite brewdo once again—I'll
have to come up with something else, I suppose.

The great part about all of this, though, is not necessarily what
I've accomplished (though I'm pretty proud of that, too!) but the
fact that I've been able to exercise my code muscles in a way I
haven't done much of in awhile.  I think there's a core truth here
about not letting oneself get dull, something I had once done with
a past employer for far too long, accepting "maintenance mode" in
the name of fiscal security.  Being able to break out, explore, see
faults (in myself or in code) and constantly improve is what feeds
me and drives me.

So, yeah, it's been a great week.  And I'm hoping that I can do it
again soon.

