# Asterisk 1.8 Installation in CentOS

Description
-----------

My machine name is ```ippbx01```, my normal user's username is ```anton```.

My Linux distro is ```CentOS 6.3 minimal```.

I use user ```root``` the whole time.

Please make sure that your CentOS is reachable from other workstations, and connected properly to the internet.

I put all source codes (*.gz files) in ```/root```, this is done by doing scp to the server using command scp, nautilus or winscp, or other methods. The point is to get the source packages uploaded to CentOS.

For those who don't where to get all required source codes, they are available in Asterisk website http://www.asterisk.org/downloads.

- [DAHDI complete](http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz)
- [LibPRI](http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz)
- [Asterisk 1.8](http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-1.8-current.tar.gz)

Update CentOS packages
----------------------

```
yum update
```

Reboot your CentOS after yum finish updating.

Install development packages
----------------------------

Please make sure that you have installed these packages correctly

```
yum install gcc gcc-c++ make kernel-devel ncurses-devel 
yum install newt-devel mysql-devel openssl-devel libxml2-devel
yum install zlib-devel wget perl
```

Make sure all the packages are in this directory (```/root```).

```
cd /root                                        
ls                                         
```

Install libpri first
--------------------

```
tar -zxvf  libpri-1.4.12.tar.gz           
cd libpri-1.4.12
make                         
make install                 
cd ../
```

A slight detour
---------------

This is due to CentOS 6.3 install different kernel name

Please make sure that the result of ```uname -r``` reflected the updated kernel version.  
This process is important to get ```DAHDI``` working properly.

```
uname -r
/usr/src/kernels
ln -s $(uname -r) 2.6.32-279.el6.i686
```

Continue to install DAHDI
-------------------------

```
tar -zxf dahdi-linux-complete-2.6.1+2.6.1.tar.gz   
cd dahdi-linux-complete-2.6.1+2.6.1
make                
make install        
make config         
cd ../
```

Finally install Asterisk
------------------------

```
tar -zxf asterisk-1.8.15.1.tar.gz          
cd asterisk-1.8.15.1
```

Please note that if you upgrade or re-compile, do these 4 steps on every upgrade:

```
./configure                
make menuconfig            
make                       
make install    
```

Do this once, if you upgrade or re-compile, do not repeat this step:

```
make samples  
```

Setup init script so that asterisk will run on every reboot:

```
cp contrib/init.d/rc.redhat.asterisk /etc/init.d/  
chkconfig --add rc.redhat.asterisk
chkconfig rc.redhat.asterisk on
vi /etc/init.d/rc.redhat.asterisk
```

Edit ```AST_SBIN``` in ```/etc/init.d/rc.redhat.asterisk``` to ```AST_SBIN=/usr/sbin```.

Disable selinux
---------------

Edit ```/etc/sysconfig/selinux``` and change ```SELINUX=enforcing``` to ```SELINUX=disabled```.

Disable iptables (for now)
--------------------------

```
chkconfig iptables off
```

Next time, you can enable it with options to unblock required ports only and block the rest.

Reboot and check if asterisk is running
---------------------------------------

Check if the process is exists:

```
ps ax | grep asterisk
```

Check if asterisk allocates network ports:

```
netstat -lnptu | grep asterisk
```

Enter Asterisk console:

```
asterisk -rvvv
```
