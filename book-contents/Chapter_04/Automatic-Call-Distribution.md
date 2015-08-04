# Automatic Call Distribution

**ACD** in short.

You need to understand how [[Ring Groups|4.6-Ring-Groups]] works first, at least experience how to configure and use a ring group.

## Edit /etc/asterisk/queues.conf

In this file we will define a queue called **techsupport**. 

Add these lines at the bottom of the file:

```
[queue_template](!)
musicclass=company      ; musics defined in /etc/asterisk/musiconhold.conf
strategy=rrmemory       ; use the Round Robin Memory strategy
joinempty=yes           ; join the queue when no members available
leavewhenempty=no       ; don't leave the queue when no members available
ringinuse=no            ; don't ring members when already InUse

[techsupport](queue_template)
maxlen = 4
member = Local/5501@internal
member = Local/5502@internal
```

Detail information about what you can setup for queues is in `/etc/asterisk/queues.conf`.

## Configure dialplan

Add these lines to the bottom of `extensions_services.conf` file:

```
exten = 2000,1,NoOp(Entering queue Tech. Support)
    same = n,Answer
    same = n,Queue(techsupport)
    same = n,Hangup
```

## Reload config

Save all files and run this command to reload:

```
asterisk -rx 'reload'
```

## Testing

You can dial ```2000``` and both extensions configured as member of queue **techsupport** should ring. The third caller and next will be put on hold in a queue.

## Asterisk commands

Several commands available for managing queue:

```
asterisk -rx 'help queue'
```

## Queue log

Queue calls recorded in both regular Asterisk log files such as `/var/log/asterisk/full` and special queue only log file `/var/log/asterisk/queue_log`.

You may use third party application such as [Asternic Call Center Stats](http://www.asternic.biz) to view the log with a nice web-based graphical interface.
