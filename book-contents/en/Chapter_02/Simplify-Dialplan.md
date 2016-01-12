# Simplify Dialplan

Example of the original, non-simplified, dialplan:

```
[office]

exten => 101,1,Verbose(Dialing 101)
exten => 101,n,Dial(SIP/101)
exten => 101,n,Hangup

exten => 102,1,Verbose(Dialing 101)
exten => 102,n,Dial(SIP/101)
exten => 102,n,Hangup

exten => 103,1,Verbose(Dialing 101)
exten => 103,n,Dial(SIP/101)
exten => 103,n,Hangup
```

Below example will have the same outcome, slightly simple then previous:

```
[office]

exten = 101,1,Verbose(Dialing 102)
    same = n,Dial(SIP/102)
    same = n,Hangup

exten = 102,1,Verbose(Dialing 102)
    same = n,Dial(SIP/102)
    same = n,Hangup

exten = 103,1,Verbose(Dialing 103)
    same = n,Dial(SIP/103)
    same = n,Hangup
```

And this is the simplified version of both dialplans above, using pattern matching and variables:

```
[office]

exten = _1XX,1,Verbose(Dialing ${EXTEN})
   same = n,Dial(SIP/${EXTEN})
   same = n,Hangup
```
