# Using virtualenv Python in local Ansible

- date: 2014-04-05 23:23
- tags: ansible, virtualenv, python

----

I've been playing with [Ansible](http://www.ansible.com)'s
[module](http://docs.ansible.com/digital_ocean_module.html) for
[DigitalOcean](https://www.digitalocean.com/?refcode=a8c32c98c565) as
I work toward moving my site off the creaky old shared hosting and
into The Future™.  I hope to have a more in-detailed writeup of
that whole process later, but right now I have it working rather
nicely with a shared playbook and roles that let me spin up my
entire system either on DigitalOcean or in
[Vagrant](https://www.vagrantup.com/).

One catch was getting local Ansible (since the `digital_ocean`
action runs on the local system) to work with the
[virtualenv](http://www.virtualenv.org/) I'm developing in.  Ansible
prefers to run actions using a specific
[Python](https://www.python.org/)—it defaults to `/usr/bin/python`—and
on my MacBook that is the system Python, which is frankly best left
alone.  I'm not only using a Python built with [Homebrew](http://brew.sh/),
but I'm also isolating the whole project in a virtualenv because
it's bad system management practice to globally install Python bits
that I'm using just for one project.  I do need one bit in particular
to use the DigitalOcean module: [dopy](https://github.com/devo-ps/dopy).
And using virtualenv makes it far easier to capture requirements
for my deployment project.

After reading about the plights of a number of people in similar
jams, I found `ansible_python_interpreter`.  This setting allows
you to specify the path of the Python interpreter Ansible will use
to run actions.  I wasn't totally sure I could use it, though,
because who knows where my virtualenvs would be checked out?  It
would never point to the right `python` binary.

The solution turned out to be mind-bogglingly simple.  My local
inventory:

````
[localhost]
localhost ansible_python_interpreter=python
````

Run this way:

````
ansible-playbook -i inventory digital_ocean.yml
````

When Ansible now executes `python` instead of `/usr/bin/python`,
since it's just running it in a subshell, it'll use the activated
virtualenv `python` binary instead, just as if you had run `python`
yourself directly from the shell.

Can't believe I banged my head against the wall for a half hour on
this.

