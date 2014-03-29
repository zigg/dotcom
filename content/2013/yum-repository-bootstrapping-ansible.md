# Yum repository bootstrapping in Ansible

- date: 2013-12-03 01:45
- tags: ansible, rpm, yum

----

I don't do cloud-scale work, but I do provision systems a lot, since
I develop and maintain a custom Linux distribution that we sell on
a box at [Code Blue][1].  With that in mind, and after hearing more
about some of the options available at the inaugural meeting of
[DevOps West Michigan][2], I decided some weeks ago to investigate
the current state-of-the-art in that field.

My short time with [Puppet][3] was marked with initial intrigue but
eventually deep frustration over a specific thing I really wanted
to do while provisioning new systems: add third-party [yum][4]
respositories.  A few of them, in fact.  Trying to do this with the
built-in [yumrepo][5] type ran me headlong into a [really old bug][6]
(which I helped reproduce), which was partly responsible for me
opting to check out [Ansible][7].

I discovered that I *really* liked Ansible, but I was still stuck
with a weird chicken-and-egg problem getting the repositories up
and running on my systems.  If I wanted to add, say, [EPEL][8] from
the shell, I'd head to [the `epel-release` download page][9] (linked
from the Fedora wiki), grab the latest RPM, and `rpm -Uvh` it.
Tedious, manual, and not very automatable.  Most users get around
this (and the problem of the release package filename's changing)
by creating their own `.repo` files in `/etc/yum.repos.d` from
scratch, but this creates an interesting situation if a wanted
package ever depends on its repository's release package—which is
exactly the case for packages in [the AsteriskNOW repo][10], since
you've now got your little custom repo and the official repos from
the release package both hanging around.

But then I stumbled across a really neat little trick.  [Stack
Overflow][11] user [ravello][12] posted about [using a `.repo` file
to *bootstrap*][13] installing a release package, without knowing
its version number—only requiring a stable repository URL and release
package name.  From that inspiration, I created a few tasks in my
Ansible playbook to bootstrap the repositories using exclusively
declarative tasks:

````yaml
- name: bootstrap epel-release install
  copy: src=ansible-bootstrap-epel.repo
        dest=/etc/yum.repos.d/
        owner=root group=root mode=0644

- name: epel-release install
  yum: name=epel-release
       enablerepo=ansible-bootstrap-epel
       state=present

- name: epel repository enable
  ini_file: dest=/etc/yum.repos.d/epel.repo
            section=epel
            option=enabled
            value=1
````

The above does three things.  First, it copies a bootstrap `.repo`
file to `/etc/yum.repos.d`.  The repository uses the official URL,
has a unique name so as not to clash with the release package's
`.repo` files, and—critically—is *disabled*.  For EPEL, it looks
like this:

````ini
[ansible-bootstrap-epel]
name = Ansible bootstrap for epel
mirrorlist = http://mirrors.fedoraproject.org/mirrorlist?repo=epel-$releasever&arch=$basearch
failovermethod = priority
enabled = 0
gpgcheck = 0
````

From there, the `enablerepo` option on the `yum` task is used to
temporarily enable the bootstrap repository long enough to install
`epel-release`.  Finally, the last task edits the new `epel.repo`
from `epel-release` to enable it, since it ships disabled.

For AsteriskNOW, a similar set of tasks:

````yaml
- name: bootstrap asterisknow-version install
  copy: src=ansible-bootstrap-asterisk-current.repo
        dest=/etc/yum.repos.d/
        owner=root group=root mode=0644

- name: asterisknow-version install
  yum: name=asterisknow-version
       enablerepo=ansible-bootstrap-asterisk-current
       state=present

- name: asterisk-11 repository enable
  ini_file: dest=/etc/yum.repos.d/centos-asterisk-11.repo
            section=asterisk-11
            option=enabled
            value=1
````

and a bootstrap `.repo` file:

````ini
[ansible-bootstrap-asterisk-current]
name = Ansible bootstrap for asterisk-current
baseurl = http://packages.asterisk.org/centos/$releasever/current/$basearch/
enabled = 0
gpgcheck = 0
````

get the same job done.

I'm pretty sure this is the most robust way to get third-party yum
repositories up and running on Ansible-managed systems.  I'm pretty
thrilled with it, in any event.  I've used it, with slight modification,
in [the Vagrant hacking setup for `octothorpe`][14].

[1]: http://www.codeblue.com/
[2]: http://www.meetup.com/DevOps-West-Michigan/
[3]: https://puppetlabs.com/
[4]: https://fedoraproject.org/wiki/Yum
[5]: https://docs.puppetlabs.com/references/latest/type.html#yumrepo
[6]: https://projects.puppetlabs.com/issues/1238
[7]: http://ansibleworks.com/
[8]: https://fedoraproject.org/wiki/EPEL
[9]: http://download.fedoraproject.org/pub/epel/6/i386/repoview/epel-release.html
[10]: http://packages.asterisk.org/
[11]: https://stackoverflow.com/
[12]: https://stackoverflow.com/users/1876318/ravello
[13]: http://stackoverflow.com/a/14155303/722332
[14]: https://github.com/zigg/octothorpe/blob/master/etc/playbook.yml
