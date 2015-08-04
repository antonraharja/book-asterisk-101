# Music On Hold

**MOH** in short.

Before setting up queues, most users wants the music played while their callers waiting in queue is their own music files, for example company jingle or product/services ads.

Edit `/etc/asterisk/musiconhold.conf` to add new music class.

Add these lines into the bottom of the file to add new music on hold class called `company`:

```
[company]
mode=files
directory=company/mohmp3
sort=random
```

Create the music on hold folder for above example at `/var/lib/asterisk/company/mohmp3`:

```
mkdir -p /var/lib/asterisk/company/mohmp3
```

Upload mp3 files to that directory. You need to at least upload/put a single mp3 file in that directory, otherwise the MOH configuration will not properly reloaded.

Reload MOH configuration:

```
asterisk -rx 'moh reload'
```

Check MOH configuration:

```
asterisk -rx 'moh show classes'
asterisk -rx 'moh show files'
```

# Setting MOH on SIP Account

Edit `/etc/asterisk/sip_friends.conf` and add option `mohsuggest=company` at the end of SIP account 101, then reload SIP configuration. That will set MOH for SIP account 101 to `company`, whenever SIP account 101 hit hold button on their softphone or IP phone the caller will hear the music from MOH class `company`.
