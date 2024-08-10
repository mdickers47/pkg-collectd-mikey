# collectd with local configuration

This was forked from https://aur.archlinux.org/collectd, and contains a
file from https://github.com/vitkabele/dsm-snmp-collectd.  My changes are:

+ include the Synology MIB files and @viktabele's mapping file.
  (see below)
+ include a `/etc/collectd.conf` that is less useless
+ the `collectd.conf` does an Include of `/etc/collectd.d/*.conf`,
  so local host modifications are easier to live with
+ include configuration to write to the local graphite server
+ fix a bit-rot build failure in nut.c, using a cherrypick patch from
  upstream collectd
+ dropped libgcrypt dependency because it has also bit-rotted and I
  don't use it so I don't care
+ make it build on armv7l (but see below)
+ conflicts with `collectd` so that you can install this on top
  and have it work

It's not a separate package of config files that depends on collectd,
for the reason that collectd claims ownership of `/etc/collectd.conf`,
which makes it impossible for another package to modify.

## adding monitoring of a Synology NAS

I can find no native package that runs collectd on the NAS.  You can
however turn on SNMPv2, and then have a different machine poll it,
such as:

```
<Plugin snmp>
  <Host "nas">
    Address "nas.serenity.tpl"
    Version 2
    Community "local"
    Collect "dsm.diskTemperature"
    Collect "dsm.diskUtilization"
    Collect "dsm.diskload"
    Collect "dsm.interface"
    Collect "dsm.interfacePackets"
    Collect "dsm.services"
    Collect "dsm.systemload"
    Collect "dsm.temperature"
    Collect "dsm.uptime"
  </Host>
</Plugin>
```

Put this in e.g. `/etc/collectd.d/synology.conf`.

## Building on armv7l

At the time I'm writing this, it is possible to build on armv7l if you
work around an autoconf "C compiler cannot create executables" failure,
which is this:

https://github.com/Morganamilo/paru/issues/1184

But beware that archlinuxarm maintenance overall is bad, and random bit
rot in the build system happens often.  See:

https://www.reddit.com/r/archlinux/comments/1e1mzpd/archlinuxarm_looks_abandoned/?sort=new
