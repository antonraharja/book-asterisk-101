# Asterisk console commands 

Entering asterisk console:

```
asterisk -r
```

or enter with higher verbosity level:

```
asterisk -rvvv
```

Exit from asterisk console by pressing ```Ctrl+C``` or run command ```quit```.

Commonly used asterisk console commands:

Commands                                       | Descriptions
---------------------------------------------- | ------------
sip show peers                                 | Display SIP peers
sip show peer [sip account]                    | Display specific SIP peer
sip show users                                 | Display SIP users
sip show user [sip account]                    | Display specific SIP user
sip show registry                              | Display SIP registers
sip show settings                              | Display SIP settings
sip set debug on/off                           | Set SIP debugging
sip set debug ip [IP address]                  | Set SIP debugging with filter by IP
sip set debug peer [sip account]               | Set SIP debugging with filter by peer name
sip reload                                     | Reload SIP configuration
dialplan show                                  | Display dialplan rules
dialplan show [dialplan context]               | Display dialplan rules on specific context
dialplan reload                                | Reload dialplan rules
core show channels                             | Display all active channels
core show settings                             | Display Asterisk configuration
core show translation                          | Display codec translation table
core show channeltypes                         | Display channel types or supported protocols
core show uptime                               | Display Asterisk uptime
core set verbose [verbosity level]             | Set verbosity level
core restart now                               | Restart Asterisk immediately
core stop now                                  | Stop Asterisk immediately
core restart when convenient                   | Restart Asterisk until all channels finished
core stop when convenient                      | Stop Asterisk until all channels finished
core restart gracefully                        | Restart Asterisk until all channels finished
core stop gracefully                           | Stop Asterisk until all channels finished
reload                                         | Reload all configuration without restart
help [Asterisk command]                        | Help for specific Asterisk command