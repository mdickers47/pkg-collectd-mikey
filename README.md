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
+ conflicts with `collectd` so that you can install this on top
  and have it work

It's not a separate package of config files that depends on collectd,
for the reason that collect claims ownership of `/etc/collect.conf`
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
