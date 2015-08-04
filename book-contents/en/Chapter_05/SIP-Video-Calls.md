# SIP Video Calls

## Edit /etc/asterisk/sip.conf

Add option to enable video in ```sip.conf```, add these lines to block ```general```:

```
videosupport=yes
```

## Edit SIP accounts

Edit each SIP account that you need the video active:

Add video codec: ```h263``` and/or ```h263p``` and/or ```h264```.

Some video phone or video softphone only able to work properly when only 1 video codec enabled. Free softphone usually only provide video codec h263 and h263p.

For example:

```
disallow=all
allow=g729
allow=ulaw
allow=h263p
```

## Example sip.conf

This is an example of sip.conf structure after enabling video support:

```
[general]
...
...
videosupport=yes
...
...

[101]
type=friend
host=dynamic
defaultuser=101
secret=112233
context=office
disallow=all
allow=g729
allow=ulaw
allow=h263p

[102]
type=friend
host=dynamic
defaultuser=102
secret=112233
context=office
disallow=all
allow=g729
allow=ulaw
allow=h263p
```
