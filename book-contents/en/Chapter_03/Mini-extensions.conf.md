# Mini extensions.conf

## Backup /etc/asterisk/extensions.conf

```
cp /etc/asterisk/extensions.conf /etc/asterisk/extensions.conf.dist
```

## Empty /etc/asterisk/extensions.conf

```
cat /dev/null > /etc/asterisk/extensions.conf
```

## Edit /etc/asterisk/extensions.conf

Add these lines into this empty extensions.conf:

```
; vi /etc/asterisk/extensions.conf

[general]

static=yes
writeprotect=no
clearglobalvars=no

[globals]

[from-external]

include = catchall

#include extensions_main.conf
```

## Reload new dialplans

Save the file and run:

```
asterisk -rx 'dialplan reload'
```

Check the new default context (as defined in `sip.conf`):

```
asterisk -rx 'dialplan show from-external'
```
