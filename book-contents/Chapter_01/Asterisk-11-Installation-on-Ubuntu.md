# Asterisk 11 Installation in Ubuntu

Description
-----------

My machine name is `ippbx01`, my normal user's username is `anton`.

My Linux distro is `Ubuntu Desktop 14.04`. But fyi this installation steps will also compatible with `Ubuntu Server 12.04` and `Ubuntu Server 14.04`. In production system you should use the server release instead of desktop.

In this training I will use user `root` the whole time.

Please make sure that your Ubuntu is reachable from other workstations, and connected properly to the internet.

I put all source codes (*.gz files) in `/home/anton`, this is done by doing scp to the server using command scp, nautilus or winscp, or other methods. The point is to get the source packages uploaded to Ubuntu.

For those who don't where to get all required source codes, they are available in Asterisk website http://www.asterisk.org/downloads.

- [DAHDI complete](http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz)
- [LibPRI](http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz)
- [Asterisk 11](http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz)

Update Ubuntu packages
----------------------

```
apt-get update
apt-get upgrade
```

Reboot your Ubuntu after apt-get finish upgrading.

Install development packages
----------------------------

Please make sure that you have installed these packages correctly

```
apt-get install build-essential zlib1g-dev libxml2-dev libncurses-dev libnewt-dev libsrtp0-dev \
linux-headers-$(uname -r) libmysqlclient-dev libsqlite3-dev libssl-dev subversion wget
```

Make sure all the packages are in this directory (`/home/anton`).

```
cd /home/anton
ls                                         
```

Install DAHDI first
--------------------

Before installing DAHDI please make sure that the result of ```uname -r``` reflected the updated kernel version and installed linux headers.

```
tar -zxf dahdi-linux-complete-current.tar.gz
cd dahdi-linux-complete-2.9.1.1+2.9.1/
make all
make install
make config
cd ../
```

Continue to install libpri
--------------------------

```
tar -zxf libpri-1.4-current.tar.gz
cd libpri-1.4.15/
make                         
make install                 
cd ../
```

Finally install Asterisk
------------------------

```
tar -zxf asterisk-11-current.tar.gz
cd asterisk-11.12.0/
```

Get the source of MP3. Of course if you don't plan to use MP3 files then you don't need to get it.

```
./contrib/scripts/get_mp3_source.sh
```

Please note that if you upgrade or re-compile always do these 4 steps on every upgrade:

```
./configure
make menuconfig
make
make install
```

Please do below command once only on the first installation. When you upgrade or re-compile Asterisk then **do not repeat this step**:

```
make samples  
```

Continue installation, next to edit `/etc/asterisk/asterisk.conf`.

Look for option `live_dangerously`, the default value on Asterisk 11 is `no` but commented so its a `yes` actually. On Asterisk 12 this option will be defaulted to `no` therefore I suggest to follow that.

Change `;live_dangerously = no` to `live_dangerously = no` (notice that semi-colon character or the comment mark is removed).

More about **live_dangerously**:

* https://wiki.asterisk.org/wiki/display/AST/Privilege+Escalations+with+Dialplan+Functions

Last, setup init script so that asterisk will run on every reboot:

```
cp contrib/init.d/rc.debian.asterisk /etc/init.d/  
update-rc.d rc.debian.asterisk defaults
```

Edit /etc/init.d/rc.debian.asterisk
-----------------------------------

```
vi /etc/init.d/rc.debian.asterisk
```

Locate these configuration and change them:

```
DAEMON=__ASTERISK_SBIN_DIR__/asterisk
ASTVARRUNDIR=__ASTERISK_VARRUN_DIR__
ASTETCDIR=__ASTERISK_ETC_DIR__
```

Change into:

```
DAEMON=/usr/sbin/asterisk
ASTVARRUNDIR=/var/run/asterisk
ASTETCDIR=/etc/asterisk
```

Start DAHDI and Asterisk manually
---------------------------------

Start DAHDI manually:

```
/etc/init.d/dahdi start
```

Start Asterisk manually:

```
/etc/init.d/rc.debian.asterisk start
```

Reboot and check if asterisk is running
---------------------------------------

Use one of these commands to reboot the server:

```
shutdown -h now
```

or

```
reboot
```

After the server up check if the Asterisk process is exists (is running):

```
ps ax | grep asterisk
```

Check if asterisk allocates network ports:

```
netstat -lnptu | grep asterisk
```

Enter Asterisk console (press ```Ctrl+C``` or command ```quit``` to exit):

```
asterisk -rvvv
```

Check DAHDI:

```
dahdi_cfg -vvv
```