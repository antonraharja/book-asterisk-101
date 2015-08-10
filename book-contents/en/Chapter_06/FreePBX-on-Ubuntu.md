# FreePBX on Ubuntu

FreePBX is free and open source software to manage Asterisk, it provides web based user interface to vanilla Asterisk installation.

Website:

* http://www.freepbx.org

## Source

Download FreePBX source **on Asterisk server**:

```
cd /root
wget -c http://mirror.freepbx.org/freepbx-2.11.0.42.tgz
```

Please note that by the time this manual written the FreePBX version used was 2.11.0.42.

## Preparation

You need `LAMP`. You already have the `L`, lets install the `AMP` for FreePBX:

```
apt-get install mysql-server mysql-client apache2 \
php5 php5-gd php5-mysql php5-cli php-pear php-db
```

Follow instructions in topic [[Call Detail Record|4.1-Call-Detail-Record]] to setup proper web server and MySQL server (and CDR too).

Add system user for Asterisk:

```
useradd -r asterisk
```

Before installing FreePBX, you must have a fully working Asterisk. Install Asterisk as described in topic [[Asterisk 11 installation on Ubuntu|1.5-Asterisk-11-installation-on-Ubuntu]].

Change web server user's into `asterisk` and also Asterisk files ownerships.

Edit `/etc/apache2/envvars` and look for these options:

```
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data
```

Change them into this:

```
export APACHE_RUN_USER=asterisk
export APACHE_RUN_GROUP=asterisk
```

Restart web server:

```
/etc/init.d/apache2 restart
```

Change Asterisk files ownership:

```
chown asterisk.asterisk -R /etc/dahdi /etc/asterisk \
/var/lib/asterisk /var/log/asterisk /var/spool/asterisk \
/usr/lib/asterisk
```

Change Apache2 and PHP files ownership:

```
chown asterisk -R /var/www /var/lock/apache2 /var/log/apache2 /var/lib/php5
```

Preparation done, continue with FreePBX installation.

## Installation

First to prepare a database for FreePBX, the database need to be created before we can continue:

```
mysqladmin -uroot -p create asterisk
```

Next to run below commands and follow instructions given at `install_amp`:

```
cd /root
tar -zxf freepbx-2.11.0.42.tgz 
cd freepbx/
./install_amp --cleaninstall --installdb
```

## Testing

Browse `http://<your web server IP>/admin/`.
