# Interactive Voice Response

**IVR** in short.

Please do tutorial in [[System Recorder|4.3-System-Recorder]] topic first, and then record a good welcome message.

The welcome message, for example, will say something like this:

```
Welcome to IT department
Please press one for free conference room
Press two for accessing our time server
Press zero for operators
```

Please note that in our example the voice is recorded as file `voice_1.wav` located in `/var/lib/asterisk/sounds`.

You can use methods in [[System Recorder topic|4.3-System-Recorder]] to record the file.

## Edit /etc/asterisk/extensions_services.conf

Add these lines to the end of the file:

```
exten = 70,1,Verbose(Accessing IVR)
    same = n,Goto(ivr-welcome,s,1)
    same = n,Hangup

[ivr-welcome]

exten = s,1,NoOp(IVR welcome)
    same = n,Answer
    same = n,Playback(silence/1)
    same = n,Set(retry=1)
    same = n(start),Background(voice_1)
    same = n,WaitExten(5)
    same = n,Set(retry=$[${retry} + 1])
    same = n,GotoIf($["${retry}" > "2"]?end:start)
    same = n(end),Hangup
    
exten = 1,1,NoOp(Free conference room)
    same = n,Answer
    same = n,Read(ROOMNUMBER,press&digits/4&digits&card-number)
    same = n,Dial(Local/7${ROOMNUMBER}@services)
    same = n,Hangup

exten = 2,1,NoOp(Time service)
    same = n,Dial(Local/88@services)
    same = n,Hangup

exten = 0,1,NoOp(Reaching operators)
    same = n,Answer
    same = n,Queue(techsupport)
    same = n,Hangup
```

## Testing

Dial **70**.

Pressing digit **1** will take you to context `ivr-welcome` exten `1`, option for conference room.

Pressing digit **2** will take you to context `ivr-welcome` exten `2`, option for listening to server time.

Pressing digit **0** will take you to context `ivr-welcome` exten `0`, option for entering queue **techsupport**.

When you choose to ignore the instruction then the instruction will be repeated at maximum 3 times before hangup.
