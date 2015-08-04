# Ring Groups

Copy these examples to the bottom of ```extensions_services.conf```.

In example 1 and 2 the phone will be ringing sequentially, one by one, with 15 seconds ring time each.

Example 1:

```
exten = 1001,1,NoOp(Operator)
    same = n,Dial(SIP/101,15)
    same = n,Dial(SIP/102,15)
    same = n,Playback(vm-nobodyavail)
    same = n,Hangup
```

Example 2:

```
exten = 1002,1,NoOp(Operator)
    same = n,Dial(Local/5501@internal,15)
    same = n,Dial(Local/5502@internal,15)
    same = n,Playback(vm-nobodyavail)
    same = n,Hangup
```

In example 3 and 4 all phones will ring all-at-one, with 20 seconds ring time.

Example 3:

```
exten = 1003,1,NoOp(Operator)
    same = n,Dial(SIP/101&SIP/102,20)
    same = n,Playback(vm-nobodyavail)
    same = n,Hangup
```

Example 4:

```
exten = 1004,1,NoOp(Operator)
    same = n,Dial(Local/5501@internal&Local/5502@internal,20)
    same = n,Playback(vm-nobodyavail)
    same = n,Hangup
```

## Testing

Dial **1001** and **1002** for sequential ring group.

Dial **1003** and **1004** for all-at-one ring group.
