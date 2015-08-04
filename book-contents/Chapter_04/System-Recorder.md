# System Recorder

## Edit /etc/asterisk/extensions_services.conf

Add these lines at the bottom of the file:

```
; demonstrates recording caller voice to a file
; recording file can be used later in dialplans, eg. for IVR

exten = 75,1,Answer
    same = n,Playback(silence/1)
    same = n,Playback(press&card-number)
    same = n,Read(NUMBER)
    same = n,Playback(say-temp-msg-prs-pound)
    same = n,Record(voice_${NUMBER}.wav,,120,k)
    same = n,Hangup
```

Information about dialplan command `Record` can be read at this URL:
https://wiki.asterisk.org/wiki/display/AST/Application_Record

Recording files location is ```/var/lib/asterisk/sounds```.

Below is the dialplan to play the recording files. Add them in ```extensions_services.conf```:

```
exten = 76,1,Answer
    same = n,Playback(silence/1)
    same = n,Playback(press&card-number)
    same = n,Read(NUMBER)
    same = n,Wait(1)
    same = n,Playback(dictate/playback)
    same = n,SayDigits(${NUMBER})
    same = n,Playback(voice_${NUMBER})
    same = n,Hangup
```

## Testing

Dial **75**, choose a number and record your voice.

Dial **76** to play the recording file.
