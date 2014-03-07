# Trust upgrades

- date: 2014-03-07 22:15
- tags: keybase, pgp, trust, crypto

----

````
$ keybase track trenton
info: ...checking identity proofs
✔ public key fingerprint: 7E3C FC83 D64E 2A7B C124 5AC9 191A 0473 89DD 2A77
✔ "trenton42" on twitter: https://twitter.com/trenton42/status/441778278521909248
✔ "trenton42" on github: https://gist.github.com/9404822
Is this the trenton you wanted? [y/N] y
Permanently track this user, and write proof to server? [Y/n]
````

I couldn't answer yes.

[Keybase](https://keybase.io) lit up many a [Twitter](https://twitter.com)
timeline this weekend, including mine.  I was lucky enough to nab
an invite via [Alex Gaynor](http://alexgaynor.net/), and so I [signed
up](https://keybase.io/zigg) and started futzing about, as is my wont.

I was first presented the option of browser-based cryptography,
something I've muttered about recently.  Keybase offers several
browser-based cryptography options, but doesn't require you to use
them.  So I didn't—I kept my encrypted private key to myself.  I
did install (via [npm™](https://www.npmjs.org/), which was my first
time using it for anything!) [the `keybase` command-line
package](https://keybase.io/__/command_line/keybase#installation)
and used that to
[prove](https://twitter.com/zigg/status/441718507026542592) that
[@zigg](https://twitter.com/zigg) is operated by me by way of [my
PGP
key](http://wwwkeys.pgp.net:11371/pks/lookup?op=get&search=0xAB46823FCFBF2B13).
I also [did the same](https://gist.github.com/zigg/9401905) for
[GitHub](https://github.com/).  So far, so good.

It's important to realize just *what* was asserted, though.  Let's
look closely at the Twitter assertion:

> Verifying myself: I am zigg on Keybase.io. j-OVTQsjeiKqyaSnOHTf56EzvgSyfKSpltl7 / https://keybase.io/zigg/sigs/j-OVTQsjeiKqyaSnOHTf56EzvgSyfKSpltl7
>
> —@zigg, https://twitter.com/zigg/status/441718507026542592

The hash utilizes my PGP key to sign the assertion that @zigg and Keybase
zigg are the same person.  So far, so good.  The GitHub proof makes a
similar assertion.

But there's a missing assertion here.  Can you spot it?

Okay, I was always terrible at suspense.  Here it is: *you have no
way of knowing that I, Matt Behrens, am the person behind all this.*

"How can this be?" you are asking now.  "You are @zigg.  I know you are.
You're that guy always talking about nerd stuff I have no hope of
comprehending and progressive rock bands I've never heard of and
[Unikitty](http://www.lego.com/en-us/movie/explore/characters/unikitty)
(who I've actually heard of and is pretty awesome.)  And you did this
thing and Keybase gave me tools I can use to verify that this proof of
yours is all above the level, I go to Keybase.io and there you are, and
there's your GitHub link and your Twitter link and it says you check
out."  A-ha, but… you never verified my PGP key.

Let me break this model for you.  Suspend your disbelief for a moment;
I'm well aware I'm not an attractive enough target to justify this level
of investment in.  But imagine anyway:

1.  Evil Zigg (he has a goatee) generated his own PGP key.

2.  Evil Zigg actually owns @zigg.

3.  I actually tweet somewhere else.  Evil Zigg copies most of it to
    @zigg which is where you see it, modifying things so they work out
    OK.

4.  Evil Zigg went ahead and signed up for Keybase with his key and
    tweeted the proof you saw above.

Or, maybe Evil Zigg just hacked my Twitter.  (I'm careful, but this is
more likely.)

Now you come around, see the proof, see me on Keybase, say "hey, this is
@zigg, I know him!  How could anyone who likes Unikitty so much be evil?"
And you go ahead and do a `keybase encrypt zigg -m 'top secret things'`
and Evil Zigg intercepts it and… game over.

How do you stop this attack?  One of two ways will do:

1.  You have personally met up with me, checked my ID, I've shared
    with you my PGP fingerprint, and I've sent you a PGP-signed email.

2.  There is a chain of trust between another party you've done this with
    and another party who's done this with me.

If one of these two applies, you can trust my key, provided it
matches what Keybase is giving you.  You can also trust my proofs;
you now *know* that I control both @zigg and zigg on GitHub.  You hae
navigated the trust model successfully.

If you try to go the other way, though, you're executing what I
call a "trust upgrade".  You are moving from a model of *less* trust
(a third-party service that can be compromised or tricked via various
means) to *more* trust (ownership of a public key by a specific
person).  Trust upgrades may make sense to the human mind, but as
all chains of trust can be reduced to their weakest link, attempting
to follow them in the wrong direction simply does not work.  Much
like water flowing downhill, chains of trust can *only* be followed
from strongest to weakest.

Going back to my tracking example before, you can perhaps see my
hesitation.  I'm actually pretty okay with saying that my friend
[Trenton](http://ikso.us/) is [@trenton42](https://twitter.com/trenton42)
and even [trenton42 on GitHub](https://github.com/trenton42).  But
I've not yet got around to verifying his PGP fingerprint.  So no
matter how many proofs I get that the possessor of his purported
PGP key has access to various services, I have not yet been assured
that the key is his.  I intend to resolve this soon, but for now,
Keybase's capabilities, while useful, do not increase my trust in
the key fingerprint I am presented with.  (Note: there's a good
discussion on [just what "tracking"
means](https://github.com/keybase/keybase-issues/issues/100) over
at GitHub, but I stub my little toe on point 2 of [malgorithm's
comment](https://github.com/keybase/keybase-issues/issues/100#issuecomment-36702483)—as
a human, I need to verify the key I've been given, and the way to
do that is with a fingerprint or an already-trusted signature,
because the other proofs provided do not rise to the strength of
the PGP key itself.)

Does this mean Keybase is an exercise in futility?  Not at all.  I
think it's an excellent idea to provide a way to add trust to
otherwise-untrusted services.  I love the idea of tying popular
service identities to PGP keys (in the correct direction, of course!)
And the CLI tool is a good deal easier to grok than the venerable
`gpg`.  I'm not a huge fan of in-browser crypto (the browser is way
too squishy), but I could see Keybase working to build trusted
plugins or native apps down the road that could mitigate those
issues and help bring strong crypto in reach of people whose heads
spin at the mere mention of PGP.  This is a critical problem that
desperately needs attention, and the more people that are aware of
crypto and can use it, the better off we all are.

But it has to be done safely.  Unless it's a high enough bar for
someone to take Twitter or GitHub account control as proof of
identity and that same someone is willing to accept man-in-the-middle
attack risks, it is a mistake to use the keys it serves up for
encryption or verification unless those keys have been verified—the
right way, with trust flowing downhill.

