# Conference Room

Conference room in Asterisk provided by 2 modules:

* MeetMe
* ConfBridge

In this session we will use **MeetMe**.

## Requirements

MeetMe requires DAHDI to be installed and compiled in asterisk properly.

Check if MeetMe module is loaded in asterisk:

```
asterisk -rx 'module show like meetme'
```

If MeetMe is not loaded then re-compile Asterisk to enable ```app_meetme```. Navigate to Asterisk source directory where you have compiled Asterisk last time and run ```make menuconfig```, select Applications, search for app_meetme, select it, save and exit. Continue with ```make``` and then ```make install```. Once done restart Asterisk.

A quick help of MeetMe command options can be read here:

```
asterisk -rx 'core show application meetme'
```

## Edit /etc/asterisk/meetme.conf

Add this line as an example room to the bottom of the file:

```
; this is example conference room number 911
; with user PIN 123 and admin PIN 89766
conf = 911,123,89766

; another room
conf = 8888
```

## Configure dialplan

Edit ```extensions_services.conf```:

```
vi /etc/asterisk/extensions_services.conf
```

Add new dialplan to serve MeetMe, add these lines to the bottom of this file:

```
; Dial 6<conf. room> to join static MeetMe room
exten = _6X.,1,NoOp(Entering MeetMe room: ${EXTEN:1})
    same = n,MeetMe(${EXTEN:1})
    same = n,Hangup

; Dial 7<4 digits room number> to join dynamic MeetMe room without PIN 
; for as long as max. half an hour, and you'll be notified on the last 5 minutes
; and the notification will be repeated every 2 minutes
; and there will be Music! when you're alone :)
exten = _7ZXXX,1,NoOp(Entering dynamic MeetMe room: ${EXTEN:1})    
    same = n,MeetMe(${EXTEN:1},idML(1800000:300000:120000))    
    same = n,Hangup
```

## Reload config

Save the file and run this command to reload configuration:

```
asterisk -rx 'reload'
```

## Testing

In above example we have define a meetme room **911** in ```/etc/asterisk/meetme.conf```, and defined dialplans to reach it.

Please ```dial 6``` followed by **meetme room number** to enter meetme room, in our case please dial ```6911```.

You may also ```dial 7``` followed by **any 4 digits number** to enter dynamic meetme room. Dynamic meet room means that admin don not need to setup the room first on `/etc/asterisk/meetme.conf`.

Check MeetMe status:

```
asterisk -rx 'meetme list'
```
