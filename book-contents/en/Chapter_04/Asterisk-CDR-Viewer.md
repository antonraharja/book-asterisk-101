# Asterisk CDR Viewer

Please make sure that you have configured and enabled [[CDR MySQL|4.1-Call-Detail-Record]] for Asterisk properly.

## Download source

Project URL:

* https://code.google.com/p/asterisk-cdr-viewer/

Source code URL:

* http://asterisk-cdr-viewer.googlecode.com/svn/trunk/asterisk-cdr-viewer-latest.tgz

Download the source code and upload the file to your asterisk server (to your /root).

## Install asterisk-cdr-viewer

```
tar -zxf /root/asterisk-cdr-viewer-latest.tgz
cp -rR asterisk-cdr-viewer /var/www/html/
```

### Edit asterisk-cdr-viewer config file

```
vi /var/www/html/asterisk-cdr-viewer/include/config.inc.php
```

Change some options to match your real setup, for example `$db_user`, `$db_pass` and `$db_name`.

## Browse

Browse your asterisk-cdr-viewer:

```
http://<your server address>/asterisk-cdr-viewer/
```

Example:

```
http://192.168.1.12/asterisk-cdr-viewer/
```
