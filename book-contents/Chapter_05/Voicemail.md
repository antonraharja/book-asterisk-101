# Voicemail

## Edit /etc/asterisk/voicemail.conf

In this file we can define mailboxes. Add these lines to the bottom of the file:

```
[office]
5501 = 1234,Phone 1 Mailbox
5502 = 8888,Phone 2 Mailbox,antonrd@gmail.com
```

In this file we defined 2 mailboxes, ```5501``` and ```5502```. Mailbox number don't have to be SIP account numbers.

Detail mailbox definition format can be read at the file it self, ```/etc/asterisk/voicemail.conf```.

Voicemail files will be saved in ```/var/spool/asterisk/voicemail/```.

## Access voicemail mailbox from dialplan

Edit ```/etc/asterisk/extensions_services.conf``` and add this dialplan.

Following updates in previous chapters then these lines should be added before context ```ivr-welcome```:

```
exten = 71,1,NoOp(Voicemail)
    same = n,Read(MAILBOX,extension)
    same = n,VoicemailMain(${MAILBOX}@office)
    same = n,Hangup
```

## Routing calls to voicemail

When callee is unavailable or busy, we can present caller with voicemail so that later callee can access the mailbox and retrieve voicemail.

Edit ```/etc/asterisk/extensions_cos.conf``` and modify context ```internal```:

```
[internal]

; adding Voicemail()
exten = _55XX,1,NoOp(Internal calls)
    same = n,Dial(SIP/1${EXTEN:2})
    same = n,Voicemail(${EXTEN}@office,u)
    same = n,Hangup
```

## Testing

Save all files and reload asterisk:

```
asterisk -rx 'reload'
```

Make 1 extension offline, and then dial that extension.
