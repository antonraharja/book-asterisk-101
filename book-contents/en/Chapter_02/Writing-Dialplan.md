# Writing Dialplan

## Location

All dialplans are loaded from ```/etc/asterisk/extensions.conf```.

## Format

Older format:

```
exten => <rule>,<priority>,<Asterisk command>
exten => <rule>,<next priority>,<next Asterisk command>
...
...
```

Example 2.5.1:

```
exten => 101,1,Verbose(Dialing 101)
exten => 101,n,Dial(SIP/101)
exten => 101,n,Hangup
```

Current format:

```
exten = <rule>,<priority>,<Asterisk command>
    same = <next priority>,<next Asterisk command>
    same = <next priority>,<next Asterisk command>
    ...
    ...
```

Example 2.5.2:

```
exten = 101,1,Verbose(Dialing 101)
    same = n,Dial(SIP/101)
    same = n,Hangup
```

## Rules and patterns

Use pattern matching to match with required rules.

About patterns:

- Pattern should start with an underscore
- Pattern use special characters to identify matches
- `X` will match with 1 number, from 0 to 9
- `Z` will match with 1 number, from 1 to 9
- `N` will match with 1 number, from 2 to 9
- `.` (a dot) will match with 1 character or more
- `[123]` is an example of a match for 1 character with number 1, 2 or 3 only

Example 2.5.3:

Examples | Descriptions
-------- | ------------
55XX     | match only with 55XX
_55XX    | match with 5500 to 5599
_55XX.   | match with _55XX rule plus one or more characters behind
_9NX.    | match with 920 plus one or more characters behind
_1ZXXXX  | match with 110000 to 119999
_1ZXXX.  | match with 11000 plus one or more characters behind

Example 2.5.4:

* Dialed number `622112` will match with `_X.` and `_XXXXXX` but not with `_XXXXX`
* Dialed number `5501` will match with `_5XXX` but not with `_5XXX.`
* Rule `_XXZX.` will match with international number 001 plus 2 more characters behind
* Rule `_NZXXX.` will match with local PSTN number but not with mobile or international
* Rule `_X8ZZXXXXX.` will match with Indonesia mobile dialed from local
* Rule `_X1XZXXXXX.` will match with Malaysia mobile dialed from local

## Dialplan variables

Commonly used variables:

Variable                | Descriptions
----------------------- | ------------
`${EXTEN}`              | ${EXTEN} will match with dialed extension
                        | 9${EXTEN} will add number 9 to ${EXTEN}
                        | ${EXTEN:1} will remove first digit of ${EXTEN}
                        | ${EXTEN:2} will remove first 2 digits of ${EXTEN}
                        | ${EXTEN:1:3} will remove first digit and get 3 digits after
`${CALLERID(num)}`      | Matched with Caller ID (CID) number
`${CALLERID(name)}`     | Matched with CID name

Example 2.5.5:

If ${EXTEN} is 5501:

Manipulation | Result
------------ | ------
9${EXTEN}    | 95501
${EXTEN:1}   | 501
${EXTEN:2}   | 01
9${EXTEN:3}  | 91
${EXTEN:1:2} | 50

User dials 021555123123:

Manipulation | Result
------------ | ------
${EXTEN}     | 021555123123
9${EXTEN}    | 9021555123123
${EXTEN:1}   | 21555123123
62${EXTEN:1} | 6221555123123
${EXTEN:0:3} | 021
${EXTEN:1:2} | 21

Example 2.5.6:

```
If caller dialed 908412345678
then a dialplan manipulation for ${EXTEN:1} will results 08412345678  
and for ${EXTEN:0:3} will result 084
```
