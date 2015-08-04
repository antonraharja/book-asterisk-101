# SIP Accounts


## Introduction

After successful installation, further more after successful the first SIP setting and testing on [[Hello Friend|1.6-Hello-World]], we want to learn more about how to actually add extensions, in this case extensions from SIP accounts, and make them call each other, provide call services and create some sort of Class of Services.

These 2 files we may call them as `main configuration file` or just `main file`:

* SIP configurations, including SIP accounts are loaded from `/etc/asterisk/sip.conf`.
* Dialplans, call routes and services are configured and loaded from `/etc/asterisk/extensions.conf`.

Instead of working directly with above main files, we will instead use other files which have been included from above files.

These 2 files below will be included by main files, we may call them as `sub files`:

* SIP accounts will be added in `/etc/asterisk/sip_friends.conf`.
* Dialplans will be added in `/etc/asterisk/extensions_main.conf`.

More about the dialplans can be found on the [[next topic|2.8-Structured-Dialplans]].

## Edit /etc/asterisk/sip.conf

From this file we will include `sip_friends.conf`.

Add this line to the bottom of the file:

```
#include sip_friends.conf
```

## Edit /etc/asterisk/sip_friends.conf

```
; vi /etc/asterisk/sip_friends.conf
; this file included by /etc/asterisk/sip.conf
; SIP accounts that will be used as extension will be configured here

[101]
type=friend
host=dynamic
context=office
defaultuser=101
secret=112233
qualify=yes
nat=force_rport,comedia
directmedia=no
disallow=all
allow=ulaw
callerid="Office Phone 1" <5501>
deny=0.0.0.0/0.0.0.0
permit=192.168.0.0/255.255.0.0

[102]
type=friend
host=dynamic
context=store
defaultuser=102
secret=112233
qualify=yes
nat=force_rport,comedia
directmedia=no
disallow=all
allow=ulaw
callerid="Store Phone 2" <5502>
deny=0.0.0.0/0.0.0.0
permit=192.168.0.0/255.255.0.0

[103]
type=friend
host=dynamic
context=warehouse
defaultuser=103
secret=112233
qualify=yes
nat=force_rport,comedia
directmedia=no
disallow=all
allow=ulaw
callerid="Warehouse Phone 3" <5503>
deny=0.0.0.0/0.0.0.0
permit=192.168.0.0/255.255.0.0
```

Save the file. Reload SIP configuration:

```
asterisk -rx 'sip reload'
```

Check your SIP accounts:

```
asterisk -rx 'sip show peers'
asterisk -rx 'sip show users'
```
