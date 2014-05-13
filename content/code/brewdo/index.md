# brewdo

----

brewdo is a wrapper for [Homebrew](http://brew.sh/) that sandboxes the
installation process, giving you the following advantages:

-   Your user account loses the rights to `/usr/local`, making it much
    harder to accidentally break your Homebrew-installed software (or
    other `/usr/local` software!)

-   Software installations will never sneak into your home directory

-   A method to cleanly share Homebrew admin rights among multiple
    users on a system.

brewdo uses [Sudo](http://www.sudo.ws/), but not to run commands
as `root`; rather, to run Homebrew under a dedicated sandbox user
account.

brewdo is developed [at GitHub](https://github.com/zigg/brewdo).

