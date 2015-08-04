# Simplify Dialplan

Example of the original, non-simplified, dialplan:

```
[office]

exten => 101,1,Verbose(Dialing 101)
exten => 101,n,Dial(SIP/101)
exten => 101,n,Hangup

exten = 102,1,Verbose(Dialing 102)
    same = n,Dial(SIP/102)
    same = n,Hangup

exten = 103,1,Verbose(Dialing 103)
    same = n,Dial(SIP/103)
    same = n,Hangup
```

Simplified dialplan, using pattern matching and variables:

```
[office]

; _1XX will match with 100 to 199

exten = _1XX,1,Verbose(Dialing ${EXTEN})
   same = n,Dial(SIP/${EXTEN})
   same = n,Hangup
```
