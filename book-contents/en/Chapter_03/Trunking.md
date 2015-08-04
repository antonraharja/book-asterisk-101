# Trunking

## SIP account type

Use ```type=friend``` for user agents or extensions. And use ```type=peer``` for SIP proxy or gateway, or to define connection to other server.

Notes:

* ```type=friend``` the **host** option must be **dynamic**, ```host=dynamic```
* ```type=peer``` the **host** must be **defined**, ```host=<ip address or hostname>```

Example SIP account for gateway (in sort: **SIP peer**):

```
[gateway-abc]
type=peer
host=192.168.1.18
context=from-trunk
qualify=20000
disallow=all
allow=ulaw
```

Note the use of option ```context``` above, it is where **incoming** call through that peer will be handled.

In above example calls **coming** to SIP peer ```gateway-abc``` will be handled by dialplans in ```from-trunk```.

## Edit /etc/asterisk/sip.conf

All SIP peers will be managed from special file included by ```sip.conf```.

Add this line to the bottom of ```sip.conf```:

```
#include sip_peers.conf
```

Edit /etc/asterisk/sip_peers.conf

Create ```sip_peers.conf```. You will add SIP peers in this file, instead of in ```sip.conf``` or ```sip_friends.conf```.

```
vi /etc/asterisk/sip_peers.conf
```

Example adding 2 SIP gateways:

```
[gateway-sg]
type=peer
host=192.168.1.18
context=from-trunk
qualify=20000
disallow=all
allow=ulaw

[gateway-id]
type=peer
host=192.168.2.77
context=from-trunk
qualify=20000
disallow=all
allow=ulaw
```

Reload SIP configuration and check for peers:

```
asterisk -rx 'sip reload'
asterisk -rx 'sip show peers'
```

## Asterisk Register

Asterisk can act as a SIP user agent registered to another Asterisk or other SIP server. This is very useful when Asterisk is behind a NAT router, especially when the NAT router is not configured with port forwarding for the Asterisk.

### Define SIP peer

In order to get Asterisk register to other server, first we need to define the SIP peer for that server.

Example, to register this Asterisk to VoIP Rakyat:

```
[voiprakyat]
type=peer
host=voiprakyat.or.id
context=from-trunk
username=211222
secret=verysecret11111
qualify=yes
canreinvite=no
disallow=all
allow=gsm
```

### Setup register

Next, add this line in ```sip.conf```, in general block:

```
register = 211222:verysecret11111@voiprakyat
```

Basic format is:

```
register = <username>:<password>@<SIP peer>
```

Check registration status:

```
asterisk -rx 'sip show registry'
```

Detail information about this, about other formats, can be read at shipped ```sip.conf``` (now its in ```/etc/asterisk/sip.conf.dist```). Look for **OUTBOUND SIP REGISTRATIONS** section.
