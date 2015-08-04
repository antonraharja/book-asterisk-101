# Asterisk Startup

Setting the maximum number of open files is important for running Asterisk in the long run.

## Edit /usr/sbin/safe_asterisk

Search for these options and change them, make sure they're not commented:

```
CONSOLE=no

NOTIFY=antonrd@gmail.com

SYSMAXFILES=262144

MAXFILES=32768
```

Save the file and restart asterisk:

CentOS:

```
/etc/init.d/rc.redhat.asterisk restart
```

Ubuntu:

```
/etc/init.d/rc.debian.asterisk restart
```
