# None of your business

- date: 2014-04-06 23:17
- tags: cloud, privacy, crypto

----

I recently had what became a sort of uncomfortable discussion with
a friend that I had to back out of.  This friend was into a new
health-oriented device, and I had a simple question: was it
cloud-based?

The answer was yes.  My friend seemed defensive about their choice,
likely due to my reputation preceding me as an advocate of controlling
one's own data.  I tried to say that I don't fault anyone for making
their own choice, and attempted to crack a little joke about how
the myriad utopian dreams I routinely go on about revolving around
breaking the cloud strangehold, but I've never actually *done*
anything about it.  Good points were made about the benefits of
crowdsourcing nutritional data, and the vendor itself makes much
ado about "community" features.  In short, I don't think my
self-deprecation worked.  I backed out, figuring I could only make
things worse at that point.

The thoughts, however, continued to burn in my soul.  What am I
advocating?  There is no doubt that we cannot revert to digital
hermitage and become islands, incommunicado with all but our closest
friends.  So much of the value we derive from the Internet is due
to contributions of many—information, entertainment, food for
thought.  Clearly, we're not going to do away with, say,
crowdsourcing—even if it often does take the form of [digital
sharecropping](http://blog.codinghorror.com/are-you-a-digital-sharecropper/)—the
value is too high and I really do treasure our collective ability
to create such a vast store of… everything.

But look at what we've done in parallel, in service of this new
world.  So many feed so much of their lives into massive data
collectives that know us better than we know ourselves.  We lust
for the convenience of having it all, anytime, anywhere, but the
only option we're given in order to get this convenience is to also
allow our silent partners the right to read it all as well.  What
they do with it, who knows.  I am sure there are genuinely good
people who will protect your private data as best as they are able,
a counterbalance against the hungry data-miners who assume everything
you provide them is theirs.  But even for the former, what happens
when their safeguards are not respected by the digital lock-pick,
or the next custodian by way of buyout?

We must go one of two ways.  One option is to completely dispense
with the notion that we can have any privacy at all—the
[Schmidt](https://en.wikipedia.org/wiki/Eric_Schmidt) option.  We
are certainly not being provided with many options for keeping our
privacy; most software today is created without the requisite tools
for making it happen.  Generally, it all sits in the clear on our
benevolent hosts' infrastructure.

The other option is the one I vastly prefer: we need to have a new
standard dictating how software developers (and not just the coders,
but going all the way to the top) respect their users' rights.  I
like to call it "the None of your Business doctrine", and it is
pretty succinct, I believe:

1.  If your users explictly wish to share publicly, they are also
    explicitly sharing with you.

2.  If your users do not explictly wish to share publicly, what they
    entrust your software with is none of your business—unless it is
    completely impossible for you to do what they ask without it.

I do worry a little bit about the escape hatch in number two; after
all, modern data-mining is often justified by "we need to do it to
provide our service."  I think that is bullshit, honestly.  We've
been able to encrypt data on the client and make it effectively
unreadable for anyone but the client on the other end for a very
long time.  Doubtless this fact has vexed many a software developer
who is concerned about these things, but they were overridden by
the unstoppable need to *do it anyway*.  And of course, hey, that
tasty data pays the bills nicely, doesn't it?

The logical conclusion to the wiggle room is simply this: if you do not
have access to the technology to not grant yourself the ability to peek
into your users' non-public data, and that technology is buildable,
*you must build it*.  Invest in it by granting your developers the
resources to work on it.  Allow them to join with the like-minded
community to make it happen.  If sufficient focus was brought to bear,
I have no doubt many of the holes that expose so many users to a breach
of their privacy could be plugged.

To circle back to the health device that prompted me to finally write on
this topic, it seems fairly clear to me that a few key design points
could make it abide quite well by these principles:

1.  Encrypt all private health data on the client, for the client.  Which
    is all of it.  This is data *about a person's personal health*; why
    *wouldn't* it be something only they should be able to read?  (But
    don't even ask yourself that question.  If they're not asking
    you to publish it, then you shouldn't be able to read it.)

2.  Don't let your software or service leak metadata.  Allow the
    user to use their software to contribute nutritional data
    anonymously, and have the software download large swathes of
    data instead of making it a short trip to your logs to figure
    out what your users are eating.

3.  Make community an end-to-end-encrypted feature.  I personally don't
    see a way you can stop from knowing which of your users are
    communicating with each other, but you can easily leverage even
    the weakest public-key crypto to keep yourself from reading what
    they're saying. 

That's how we should all want software to be built.  Of course,
it's not built that way today.  And some things are more important
than ideals, for now—an unfortunate but completely understandable
tradeoff.  But going forward?  Absolutely.  Let's change this.

Finally, to my friend, if you're reading this: I'm sorry.  It was
never my intent to offend.  I just want the world to respect our
boundaries, and—though I try not to be "that guy"—it hurts when
they don't.

