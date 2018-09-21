# Review: end-to-end encrypted notes with Standard Notes 

- date: 2018-03-25 12:00
- tags: review
- canonical_url: https://spin.atomicobject.com/2018/03/25/standard-notes-review/

----

I’ve been looking for a software solution I can trust for writing, journaling, and taking notes securely. Many options exist, but they never quite fulfilled the demands of my wishlist: multi-device, cloud-synced, end-to-end-encrypted, and open.

A few months ago, though, I discovered [Standard Notes](https://standardnotes.org/), and now I can’t imagine accepting any other solution.

Standard Notes feels like the kind of solution I’d engineer if I were calling all the shots. The service is entirely open-source, to the point that you can self-host it. It’s simple by default, giving you exactly and only what you need. It stores only end-to-end encrypted blobs of data, meaning the server never has access to your data. The software takes pains to protect your data against loss. And despite all this nerd-tier stuff, it’s very easy to get started.

As of this writing, you can sign up for the free tier on their website and start using Standard Notes immediately, with unlimited cloud-synced note storage and access to all the clients—web, mobile, and desktop. It’s almost too simple to mention.

One of the most useful features you get, even with the free tier, is [Device Storage Encryption](https://listed.standardnotes.org/@sn/824/your-unencrypted-data-never-touches-a-hard-drive-with-device-storage-encryption). In short, this means that even if you’re using [full-disk encryption](https://spin.atomicobject.com/2017/02/06/security-hygiene/#use-full-disk-encryption), there’s an extra layer of security to make sure that your keys are never stored unencrypted on the system, and your notes are securely encrypted whenever the app is closed. All you need to do is enable Passcode Lock in your account settings on the desktop to get this support; on iOS, just turn on Storage Encryption, and maybe Fingerprint Lock while you’re in there.

The free tier doesn’t give you access to any extensions, but it does give you the aforementioned unlimited note storage and the standard plain-text editor. I installed apps on my iPhone and my MacBook to start, turning on DSE to give my notes extra protection.

I really like having a place where I can just write…anything. Scratch space for writing something that I’m going to publish or send to someone. A quick outline of a brain dump someone is sharing. Private thoughts, journaling happenings in my life. I can do all of this on my desktop or on my phone, depending on where I am, at any time.

I never have to worry about what I write living on someone else’s server, protected by _their_ encryption keys—everything is always under the keys only _I_ have. [Writing with this freedom](https://listed.standardnotes.org/@sn/822/the-unexpected-benefits-of-encrypted-writing) is something you can’t get with other cloud-based solutions that access and/or store your unencrypted content. With this solid, secure architecture in place, I even felt comfortable recommending Standard Notes to my therapist for other patients who might find it useful for journaling.

I ran with this setup for probably a week before I decided that although I was perfectly happy with it, I wanted to both support the project and get easy access to those extensions.

[Standard Notes extensions](https://standardnotes.org/extensions) are for the desktop and web apps specifically. They run the gamut from Markdown, HTML, and Vim-emulating code editors to to-do lists and themes, as well as automatic sync, backup features, and even a feature that lets you publish selected notes to a blog.

I’m personally only using the [Advanced Markdown Editor](https://standardnotes.org/extensions/advanced-markdown), which formats your documents live as you use Markdown conventions and offers a live preview option besides. Whatever extensions you’ve used are automatically available wherever you use the web or desktop apps, so when I added Standard Notes to the inexpensive Windows 10 laptop I picked up last year, everything worked exactly the same way it did on my MacBook.

Supporting Standard Notes feels different from subscribing to many other software services. I can actually do just about everything myself—it’s all on GitHub (including the extensions!) and I could certainly self-host it all. But I feel compelled to support this project because it’s been desperately needed in the world, filling a niche that hasn’t been adequately explored, and doing so in an amazingly open way. Its existence is a dream come true for me, and I want to make sure it’s sustainable.

If you’re looking for a place to do your writing, note-taking, or journaling, I strongly suggest you take a look at Standard Notes. I was amazed that it existed when I found it, and I’m a dedicated user and proud supporter now.

_This post originally appeared [on Atomic Spin](https://spin.atomicobject.com/2018/03/25/standard-notes-review/)._
