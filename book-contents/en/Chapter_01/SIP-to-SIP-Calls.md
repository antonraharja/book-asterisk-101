# SIP to SIP Calls

We are advancing the [Hello-Friend](Hello-Friend.md).

Prepare 3 SIP devices (SIP IP Phones or Softphone), we will configure 3 SIP phones with 2 different dialplan contexts.

## Edit /etc/asterisk/sip.conf

Define 3 new SIP accounts. Go to the bottom of this file and add below lines:

```
[101]
type=friend
context=office
defaultuser=101
secret=112233
host=dynamic
qualify=yes
disallow=all
allow=ulaw
callerid="Office Phone 1" <101>

[102]
type=friend
context=office
defaultuser=102
secret=112233
host=dynamic
qualify=yes
disallow=all
allow=ulaw
callerid="Office Phone 2" <102>

[103]
type=friend
context=store
defaultuser=103
secret=112233
host=dynamic
qualify=yes
disallow=all
allow=ulaw
callerid="Office Phone 3" <103>
```

Once done editing the ```sip.conf``` you must **reload** the new SIP configuration:

```
asterisk -rx 'sip reload'
```

## Edit /etc/asterisk/extensions.conf

Define  2 new contexts, ```office``` and ```store```. Go to the bottom of this file and add these lines:

```
[office]

include = default

exten = 101,1,Dial(SIP/101)
exten = 102,1,Dial(SIP/102)
exten = 103,1,Dial(SIP/103)

[store]

include = office

```

Once done editing the ```extensions.conf``` you must **reload** the new dialplan configuration:

```
asterisk -rx 'dialplan reload'
```

Below command can be used to view the dialplan `office` that is already loaded by Asterisk:

```
asterisk -rx 'dialplan show office'
```
