# Mini sip.conf

## Backup /etc/asterisk/sip.conf

```
cp /etc/asterisk/sip.conf /etc/asterisk/sip.conf.dist
```

## Empty /etc/asterisk/sip.conf

```
cat /dev/null > /etc/asterisk/sip.conf
```

## Edit /etc/asterisk/sip.conf

Add these lines below to this empty sip.conf:

```
; vi /etc/asterisk/sip.conf

[general]

udpbindaddr=0.0.0.0
tcpenable=yes
tcpbindaddr=0.0.0.0
transport=udp,tcp

videosupport=yes
alwaysauthreject=yes
nat=auto_force_rport,auto_comedia

context=from-external

#include sip_friends.conf
```

## Reload SIP configuration

Save the file and reload SIP configuration by restarting asterisk, please run:

```
/etc/init.d/rc.debian.asterisk restart
```

Please note that you need to restart asterisk as we change the bind address and enabling TCP.

Check whether the UDP and TCP port for SIP is up:

```
netstat -lnptu | grep asterisk | grep 5060
```
