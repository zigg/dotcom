# brewdo

----

brewdo is a wrapper for [Homebrew](http://brew.sh/) that sandboxes the
installation process, giving you the following advantages:

-   Your user account loses the rights to `/usr/local`, making it
    much harder to accidentally break your Homebrew-installed
    software (or other `/usr/local` software!)

-   Software installations lose the rights to your home directory,
    meaning software will never partially install into your home
    directory

-   A clean way to share Homebrew admin rights among multiple users
    of the same system

brewdo uses [Sudo](http://www.sudo.ws/), but not to run commands
as `root`; rather, to run Homebrew under a dedicated sandbox user
account.

brewdo grew out of the experiment in my blog post, [Sandboxing
Homebrew](https://www.zigg.com/2014/sandboxing-homebrew.html), and
is developed [at GitHub](https://github.com/zigg/brewdo).

