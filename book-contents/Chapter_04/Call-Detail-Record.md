# Call Detail Record

## CDR MySQL

### Check cdr MySQL extension for asterisk

```
ls -l /usr/lib/asterisk/modules/cdr* | grep mysql
asterisk -rx "module show like mysql"
```

Above commands should show if ```cdr_mysql.so``` is exists and CDR MySQL backend is loaded.
If the answer to both is not, then you need to re-compile asterisk from source and make sure on make menuconfig you enabled cdr_mysql in ADDON tab.

### Install MySQL server and other tools

CentOS:

```
yum install mysql-server mysql-client httpd php php-mysql
```

Ubuntu:

```
apt-get install mysql-server mysql-client apache2 php5 php5-mysql
```

Once installed, please initialize MySQL first:

CentOS:

```
/etc/init.d/mysqld start
```

Ubuntu:

```
/etc/init.d/mysql start
```

Change MySQL root password:

```
/usr/bin/mysqladmin -u root -p password '<new password>'
```

Example to set the root password into **verysecret!**:

```
/usr/bin/mysqladmin -u root -p password 'verysecret!'
```

Run HTTP server, on CentOS:

```
/etc/init.d/httpd start
```

Ubuntu:

```
/etc/init.d/apache2 start
```

Check HTTP server by browsing your server's IP.

Now please make sure that both MySQL server and HTTP server will be started on boot:

CentOS:


```
chkconfig httpd on
chkconfig mysqld on
```

Ubuntu:

```
update-rc.d apache2 defaults
update-rc.d mysql defaults
```

### Create database and insert SQL for CDR

Create database 'asteriskcdrdb':

```
/usr/bin/mysqladmin -u root -p create asteriskcdrdb
```

Create new file ```/root/cdr_database.sql``` and fill it with below content:

```
CREATE TABLE cdr ( 
	id INT(11) UNSIGNED NOT NULL AUTO_INCREMENT,
        calldate datetime NOT NULL default '0000-00-00 00:00:00', 
        clid varchar(80) NOT NULL default '', 
        src varchar(80) NOT NULL default '', 
        dst varchar(80) NOT NULL default '', 
        dcontext varchar(80) NOT NULL default '', 
        channel varchar(80) NOT NULL default '', 
        dstchannel varchar(80) NOT NULL default '', 
        lastapp varchar(80) NOT NULL default '', 
        lastdata varchar(80) NOT NULL default '', 
        duration int(11) NOT NULL default '0', 
        billsec int(11) NOT NULL default '0', 
        disposition varchar(45) NOT NULL default '', 
        amaflags int(11) NOT NULL default '0', 
        accountcode varchar(20) NOT NULL default '', 
        uniqueid varchar(32) NOT NULL default '', 
        userfield varchar(255) NOT NULL default '',
        peeraccount varchar(20) NOT NULL default '',
        linkedid varchar(32) NOT NULL default '',
        sequence int(11) NOT NULL default '0',
  	PRIMARY KEY  (`id`),
  	INDEX `calldate` (`calldate`),
  	INDEX `src` (`src`),
  	INDEX `dst` (`dst`)
);
```

Referrence to CDR database:

* https://wiki.asterisk.org/wiki/display/AST/MySQL+CDR+Backend

Insert SQL statement in ```/root/cdr_database.sql``` into database ```asteriskcdrdb```:

```
mysql -u root -p asteriskcdrdb < /root/cdr_database.sql
```

### Edit /etc/asterisk/cdr_mysql.conf

Add these lines at the bottom of the file:

```
[global]
hostname=localhost
dbname=asteriskcdrdb
table=cdr
password=verysecret!
user=root
port=3306
```

Please note that you need to change the values according to your real setup.

If you're done editing, save the file and restart asterisk:

CentOS:

```
/etc/init.d/rc.redhat.asterisk restart
```

Ubuntu:

```
/etc/init.d/rc.debian.asterisk restart
```

### Do some checking

Run this command to verify that your asterisk is connected to MySQL for CDR:

```
asterisk -rx 'cdr mysql status'
```

You should see that asterisk is connected to database ```asteriskcdrdb@localhost```.

Make some calls, and then login to database to check if asterisk successfully writing CDR to your database.

Logging in to MySQL:

```
mysql -u root -p asteriskcdrdb
```

Run these SQL commands inside MySQL:

```
select * from cdr limit 5;
select calldate,src,dst,duration from cdr limit 1;
exit
```
