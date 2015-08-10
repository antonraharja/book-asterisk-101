# Fail2Ban

## Install Fail2Ban

Prior to installing fail2ban you should install mail server Postfix or Sendmail.

CentOS:

```
yum install postfix
yum install fail2ban
```

Ubuntu:

```
apt-get install postfix
apt-get install fail2ban
```

Please note that you must enable [Asterisk logger](../Chapter_02/Asterisk-Log-Files.md) before continue setting up fail2ban.

Also make sure that Postfix is configured properly.

## Configuration files 

### Filter

fail2ban filter for Asterisk located in ```/etc/fail2ban/filter.d/asterisk.conf```.

In case the file does not exists create new file with content as follow:

```
# Fail2Ban configuration file
#
#
# $Revision: 251 $
#

[INCLUDES]

# Read common prefixes. If any customizations available -- read them from
# common.local
before = common.conf


[Definition]

#_daemon = asterisk

# Option:  failregex
# Notes.:  regex to match the password failures messages in the logfile. The
#          host must be matched by a group named "host". The tag "<HOST>" can
#          be used for standard IP/hostname matching and is only an alias for
#          (?:::f{4,6}:)?(?P<host>\S+)
# Values:  TEXT
#
# Asterisk 1.8 uses Host:Port format which is reflected here

failregex = NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - Wrong password
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - No matching peer found
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - No matching peer found
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - Username/auth name mismatch
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - Device does not match ACL
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - Peer is not supposed to register
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - ACL error (permit/deny)
            NOTICE.* .*: Registration from '.*' failed for '<HOST>:.*' - Device does not match ACL
            NOTICE.* .*: Registration from '\".*\".*' failed for '<HOST>:.*' - No matching peer found
            NOTICE.* .*: Registration from '\".*\".*' failed for '<HOST>:.*' - Wrong password
            NOTICE.* <HOST> failed to authenticate as '.*'$
            NOTICE.* .*: No registration for peer '.*' \(from <HOST>\)
            NOTICE.* .*: Host <HOST> failed MD5 authentication for '.*' (.*)
            NOTICE.* .*: Failed to authenticate user .*@<HOST>.*
            NOTICE.* .*: <HOST> failed to authenticate as '.*'
            NOTICE.* .*: <HOST> tried  to authenticate with nonexistent user '.*'
            NOTICE.* .*: Call from '.*' (<HOST>:.*) to extension '.*' rejected .*
            VERBOSE.*SIP/<HOST>-.*Received incoming SIP connection from unknown peer
   
# Option:  ignoreregex
# Notes.:  regex to ignore. If this regex matches, the line is ignored.
# Values:  TEXT
#
ignoreregex =
```

## Jail

Save original ```jail.conf``` and create new one:

```
mv /etc/fail2ban/jail.conf /etc/fail2ban/jail.conf.dist
touch /etc/fail2ban/jail.conf
```

Edit new ```jail.conf```:

```
[DEFAULT]

ignoreip = 127.0.0.1/8

[asterisk-iptables]

enabled  = true
filter   = asterisk
action   = iptables-allports[name=AsteriskIPPBX, protocol=all]
           sendmail-whois[name=AsteriskIPPBX, dest=antonrd@gmail.com, sender=alarm-noreply@ngoprek.org]
logpath  = /var/log/asterisk/messages
maxretry = 10
bantime = 259200
```

You are also encouraged to add more jails copied from `/etc/fail2ban/jail.conf.dist`, for example for protecting SSH.

## Running fail2ban

Start fail2ban:

```
/etc/init.d/fail2ban start
```

Stop fail2ban:

```
/etc/init.d/fail2ban stop
```

## Monitoring

Fail2ban log file located in ```/var/log/fail2ban.log```.

Monitoring:

```
tail -f /var/log/fail2ban.log | tee today.log
```

## List banned IP address

Use ```iptables``` to view banned IP addresses list:

```
iptables -L
iptables -t filter -L
iptabels -L -n
```

## Removing banned IP address

Use ```iptables``` to remove specific IP address:

```
iptables -D fail2ban-AsteriskIPPBX -s <banned IP address> -j REJECT
```

You can restart fail2ban to remove all IP addresses.
