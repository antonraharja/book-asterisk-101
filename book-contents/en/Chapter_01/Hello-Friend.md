# Hello Friend

This is *Hello Friend* for Asterisk, the first setting and testing of SIP extension calling a service.

Edit /etc/asterisk/sip.conf
---------------------------

Go to the bottom of this file and add these lines:

```
[100]
type=friend
context=default
defaultuser=100
secret=112233
host=dynamic
qualify=yes
disallow=all
allow=ulaw
callerid="Office Phone Test" <100>
```

Reload SIP configuration
------------------------

```
asterisk -rx "sip reload"
```

Configure user agent
--------------------

Configure your device, a softphone or IP phone, to use username **100** and password **112233**.

Please make sure that you saw the status Registered (or Ready) in your device.

Check registration status in server
-----------------------------------

```
asterisk -r
```

Above command will make you enter Asterisk console. Run this command inside Asterisk console.

```
sip show peers
```

Other way to do it is by running it using a single line of command from Linux console:

```
asterisk -rx "sip show peers"
```

Dial
----

From the registered SIP device please dial **1000**.
