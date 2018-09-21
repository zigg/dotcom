# Setting up Windows to build and run Node.js applications

- date: 2018-06-18 12:00
- tags: javascript, windows, node
- canonical_url: https://spin.atomicobject.com/2018/06/18/windows-node-js/

----

[Node.js](https://nodejs.org/en/) is just JavaScript, right? So it should be really easy to run Node.js applications on Windows—just download and install Node, `npm install`, and go, right?

Well, for some applications, that’s true. But if you need to compile extensions, you’ll need a few more things. And, of course, with Node.js itself being constantly under development, you’ll want to lock down your development to a version your code can use. In this post, I’ll talk you through how we get our Windows command-line environments set up for the Node.js (actually, [Electron](https://electronjs.org/)) application my team is developing.  

First Things First
------------------

No one wants to waste time hunting down downloads for a development environment. Instead, [install Scoop first](https://spin.atomicobject.com/2017/12/20/windows-command-line-installer/), and you’ll get a nice, clean way to add the packages you’ll need without a single web search.

Once you’ve got Scoop installed, it’s time to add some packages. For just Node.js, you’ll want the nodejs package, plus nvm for version management with [NVM](https://github.com/creationix/nvm):

```powershell
scoop install nodejs nvm
```

If your project uses [Yarn](https://yarnpkg.com/en/), as ours does, you can grab that from Scoop, as well:

```powershell
scoop install yarn
```

If you’re planning on checking out or committing code to GitHub, you’ll also want tools for that:

```powershell
scoop install openssh git
```

To finish setting up [Git](https://git-scm.com/) with [OpenSSH](https://www.openssh.com/), note the post-install message that tells you to set up the `GIT_SSH` environment variable.

Finally, in case you want to quickly do things as an administrative user (which you may, later in this post!), I recommend you install Sudo, which knows how to elevate privileges inside a PowerShell session without spawning a brand new one:

```powershell
scoop install sudo
```

Managing Node.js versions
-------------------------

The next thing you’ll want to do is make sure you’re on the right version of Node.js for your project. We’re using the latest LTS version for ours, which as of the time of this writing is 8.11.2. So we issue two NVM commands to install and use it:

```powershell
nvm install 8.11.2
nvm use 8.11.2
```

If you’re familiar with NVM on Unix-like systems, you’ll find it works a little differently on Windows with Scoop. When you use a new Node.js version, it will update the binaries under `scoop\apps\nvm` instead of in `$HOME/.nvm`.

If you use a version and it doesn’t seem to be taking effect, check your `PATH` environment variable in the System Properties control panel (search for “environment”); it’s probably been re-ordered. Move the path containing `scoop\apps\nvm` to the top, and the NVM-selected version will now take precedence.

Compiling Extensions
--------------------

We don’t have any of our own extensions that need building in our project, but some of our dependencies (namely, node-sass) do.

Extensions like these are built with [node-gyp](https://github.com/nodejs/node-gyp), and node-gyp needs two things: [Python](https://python.org/) (2… wince) and a C compiler, neither of which are standard equipment on a Windows system. If you don’t have them and you need them to build extensions, you will see a long string of `gyp ERR!` messages when you install dependencies.

Thankfully, there’s a reasonably easy way to install them already configured for node-gyp: [windows-build-tools](https://www.npmjs.com/package/windows-build-tools).

After you’ve installed the Scoop nodejs package above, and assuming you installed Sudo, you can now run:

```powershell
sudo npm install --global --production windows-build-tools
```

Note that we have observed these installers rebooting a system at least once, which effectively aborted the process. We fixed this in this one case by re-running the installer like so:

```powershell
sudo npm uninstall --global windows-build-tools
sudo npm install --global --production windows-build-tools
```

The Moment of Truth
-------------------

If all the installations worked, you should be ready to go. For our application, a

```powershell
yarn install
yarn start
```

was all we needed—of course, you’ll want to start your application however you do normally.

In our case, our application started up and we were off and running.

_This post originally appeared [on Atomic Spin](https://spin.atomicobject.com/2018/06/18/windows-node-js/)._
