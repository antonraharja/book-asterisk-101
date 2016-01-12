# Structured Dialplans

## Introduction

As explained in [[SIP Accounts|SIP-Accounts.md]], instead of working directly with the main file we should instead include other files and work with them. In this topic the dialplan contained in `extensions.conf` will be written on different files and those files included by `extensions.con`.

Each files will contain specific dialplans.

## Prepare dialplan files

Create 5 additional configuration files in `/etc/asterisk`:

```
cd /etc/asterisk
touch extensions_main.conf extensions_phones.conf extensions_trunks.conf extensions_services.conf extensions_cos.conf
```

## Edit /etc/asterisk/extensions.conf

From this file we will include ```extensions_main.conf```.

Add this line to the bottom of the file:

```
#include extensions_main.conf
```

## Edit /etc/asterisk/extensions_main.conf

```
; vi /etc/asterisk/extensions_main.conf
; this file included by /etc/asterisk/extensions.conf

#include extensions_phones.conf
#include extensions_trunks.conf
#include extensions_services.conf
#include extensions_cos.conf

; Please make sure that above files are exists in /etc/asterisk
; If they don't exists then you need to create them
```

## Edit /etc/asterisk/extensions_phones.conf

```
; vi /etc/asterisk/extensions_phones.conf
; this file included by /etc/asterisk/extensions_main.conf
; in this file we configured 2 contexts that defines group of extensions
; in our case group of SIP accounts

; [office]
; exten = 101,1,Dial(SIP/101)
; exten = 102,1,Dial(SIP/102)

; We can use patterns as well
; [office]
; exten = _1XX,1,Dial(SIP/${EXTEN})

; SIP account number/ID has nothing to do technically with extension number
; just because the SIP account is 101 doesn't mean it can be called by dialing 101
; here we configured to connect with SIP 101 caller should dial 5501 instead
; [office]
; exten = _55XX,1,Dial(SIP/1${EXTEN:2})

[office]

; this context includes context 'services' 
; loaded from /etc/asterisk/extensions_main.conf
include = services

; and also includes Class of Services contexts
; loaded from /etc/asterisk/extensions_cos.conf
include = pstn-local
include = pstn-long-distance
include = mobile
include = international
include = internal
include = catchall

[store]

include = services
include = pstn-local
include = pstn-long-distance
include = internal
include = catchall

[warehouse]

include = internal
include = catchall

; don't forget to activate new dialplan configuration by reloading it
; from Linux console run: asterisk -rx "dialplan reload"
```

## Edit /etc/asterisk/extensions_trunks.conf

```
; vi /etc/asterisk/extensions_trunks.conf
; this file included by /etc/asterisk/extensions_main.conf
; in this file we configured 1 context that defines dialplans for incoming calls from trunks

[from-trunk]

exten = _X.,1,Verbose(Incoming call from outside forwarded to operator 5501)
    same = n,Dial(SIP/5501)
```

## Edit /etc/asterisk/extensions_services.conf

```
; vi /etc/asterisk/extensions_services.conf
; this file included by /etc/asterisk/extensions_main.conf
; in this file we configured call services context
; the context may be included by contexts in /etc/asterisk/extensions_phone.conf

[services]

; provides time
exten = 88,1,Answer
    same = n,Playback(silence/1)
    same = n,SayUnixTime()
    same = n,Hangup

; provides "noise" tests, by noise means monkeys yelling at caller
exten = 89,1,Answer
    same = n,Playback(silence/1)
    same = n,Playback(tt-monkeys)
    same = n,Hangup

; demonstrates recording caller voice to a file
exten = 77,1,Answer
    same = n,Playback(silence/1)
    same = n,Playback(say-temp-msg-prs-pound)
    same = n,Record(temp_${CALLERID(num)}.wav)
    same = n,Wait(1)
    same = n,Playback(temp_${CALLERID(num)})
    same = n,Hangup

; demonstrates playing recorded caller voice above
exten = 78,1,Answer
    same = n,Playback(silence/1)
    same = n,Playback(temp_${CALLERID(num)})
    same = n,Hangup
```

## Edit /etc/asterisk/extensions_cos.conf

```
; vi /etc/asterisk/extensions_cos.conf
; this file included by /etc/asterisk/extensions_main.conf
; in this file we configured Class of Services contexts that defines call routes
; these contexts may be included by contexts in /etc/asterisk/extensions_phone.conf

[internal]

exten = _55XX,1,Verbose(Internal calls)
    same = n,Dial(SIP/1${EXTEN:2})
    same = n,Hangup

[pstn-local]

exten = _9N.,1,Dial(DAHDI/g0/${EXTEN:1})

[pstn-long-distance]

exten = _90[2-7].,1,Dial(SIP/192.168.1.12/${EXTEN:1})

[mobile]

exten = _908.,1,Dial(SIP/192.168.1.12/${EXTEN:1})

[international]

exten = _900Z.,1,Dial(SIP/192.168.1.12/+${EXTEN:3})

[catchall]

exten = _X.,1,Playback(silence/1&an-error-has-occurred)
    same = n,Congestion

exten = s,1,Playback(silence/1&an-error-has-occurred)
    same = n,Congestion
```
