# Command injection, or how /bin/sh can rm -rf your server

- date: 2016-08-29 13:00
- tags: injection, security, programming

----

I've see a lot of people make the mistake of using
[system(3)](http://man.openbsd.org/system.3). That's just it, they
use it. In this
[video](https://spin.atomicobject.com/2016/07/29/command-injection-vulnerability/)
(!) over at [Atomic Spin](https://spin.atomicobject.com/), I talk
about why that's a mistake.

