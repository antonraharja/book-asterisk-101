# Asterisk Log Files

## Log location

```
cd /var/log/asterisk
```

## Log configuration

```
vi /etc/asterisk/logger.conf
```

Find option `dateformat` and remove comment mark for (this one only, not both line):

```
dateformat=%F %T
```

Find option full and remove comment mark for:

```
full => notice,warning,error,debug,verbose,dtmf,fax
```

In Asterisk 11 there is a slight difference in the phrase. Only for Asterisk 11 we're not finished yet, we need to make some changes to the verbose phrase. Update above line into:

```
full => notice,warning,error,debug,verbose(3),dtmf,fax
```

Note that I put `(3)` near the `verbose` phrase. It means that Asterisk will log in **verbosity level 3** to the `full` file.

Save the file, exit and run this to load log configuration:

```
asterisk -rx "logger reload"
```

## Watching log file

```
tail -f /var/log/asterisk/full | tee /root/log1.txt
```

Above command will save what we saw on the monitor to file ```/root/log1.txt```

Admin may later analyze `/root/log1.txt` and share them with others or experts when they need to, instead of sharing the whole full log

Please note that `log1.txt` location can be in any path admin wants, it doesn't have to be in /root/

## Understanding log entries

Example:

```
[2013-01-17 09:29:33] VERBOSE[1556] pbx.c:     -- Executing [77@office:3] Playback("SIP/101-0000000a", "say-temp-msg-prs-pound") in new stack
[2013-01-17 09:29:33] VERBOSE[1556] file.c:     -- <SIP/101-0000000a> Playing 'say-temp-msg-prs-pound.ulaw' (language 'en')
[2013-01-17 09:29:37] VERBOSE[1556] pbx.c:     -- Executing [77@office:4] Record("SIP/101-0000000a", "temp_5501.wav") in new stack
[2013-01-17 09:29:37] VERBOSE[1556] file.c:     -- <SIP/101-0000000a> Playing 'beep.gsm' (language 'en')
```

Explanation:

* We can see there are 4 entries
* Above entries shown 2 commands executed sequentially, first the Playback command playing file say-temp-msg-pres-pound.ulaw followed by Record that recording voice to temp_5501.wav
* Activity or log ID for above entries is 1556 (you can see it on 2nd column)
* The activity occurred on 2013-01-17 09:29:33 to 09:29:37

You can use combination command below to filter above entries from log file:

```
cat /var/log/asterisk/full | grep 1556
```

```
cat /var/log/asterisk/full | grep 2013-01-17
```

```
cat /var/log/asterisk/full | grep "2013-01-17 09:29"
```

```
cat /var/log/asterisk/full | grep 2013-01-17 | grep 1556
```

You can redirect filtered results to a file to save them for later readings:

```
cat /var/log/asterisk/full | grep 2013-01-17 | grep 1556 > log1-test.txt
```

You can monitor and redirect the result to a file:

```
tail -f /var/log/asterisk/full | tee log1-test.txt
```

## Log rotation

### Logrotate configuration

Logrotate location:

```
cd /etc/logrotate.d
```

### Create for Asterisk

```
touch /etc/logrotate.d/asterisk
```

Our purpose is to set log rotation for log files and CDR csv files.

```
vi /etc/logrotate.d/asterisk
```

Add these lines to the empty `/etc/logrotate.d/asterisk` file:

```
/var/log/asterisk/* {
  missingok
  rotate 1000
  daily
  create 0600 root root
  postrotate
    /usr/sbin/asterisk -rx 'logger reload' > /dev/null 2>&1
  endscript
}

/var/log/asterisk/cdr-csv/* {
  missingok
  rotate 1000
  daily
  create 0600 root root
}
```

Save the file, and run this command to activate logrotate configuration:

```
logrotate /etc/logrotate.conf
```
