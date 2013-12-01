# Disabling hibernation in PolicyKit

- tags: linux, notes
- date: 2012-12-09 20:45

----

To disable hibernation in PolicyKit, create a file
`/etc/polkit-1/localauthority/50-local.d/com.zigg.disable-hibernate.pkla`
with the following contents:

    [Disable hibernate by default]
    Identity=unix-user:*
    Action=org.freedesktop.upower.hibernate
    ResultActive=no

*Inspired by the enable configuration at [How do I hibernate my
computer?](https://help.ubuntu.com/12.04/ubuntu-help/power-hibernate.html).*

