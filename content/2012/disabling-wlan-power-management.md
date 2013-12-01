# Disabling `wlan` power management

- date: 2012-12-09 20:45
- tags: linux, notes

----

Disabling power management on a `wlan` device is easy in the one-off case:

    # /sbin/iwconfig wlan0 power off

However, `pm-utils` will re-enable power management when certain events
occur, such as disconnecting AC power.  To unconditionally keep power
management off, create `/etc/pm/power.d/wireless` as an executable script
with the following contents:

    #!/bin/sh
    /sbin/iwconfig wlan0 power off

