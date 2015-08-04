Setting IP Address in CentOS
============================

Edit /etc/sysconfig/network
---------------------------

Below is an example of ```/etc/sysconfig/network```:

```
NETWORKING=yes
HOSTNAME=ippbx01.somedomain.lan
GATEWAY=192.168.1.1
```

Edit /etc/sysconfig/network-scripts/ifcfg-eth0
----------------------------------------------

Change ```BOOTPROTO=dhcp``` to ```BOOTPROTO=static```

Add 2 new lines:

```
IPPADDR=192.168.1.12
NETMASK=255.255.255.0
```

Please change the IP address and netmask above to suit your real situation.

Edit /etc/hosts
---------------

Add this line, please make sure your hostname and IP are correct:

```
192.168.1.12 ippbx01.somedomain.lan ippbx01
```

Edit /etc/resolv.conf
---------------------

Please make sure that the DNS address is correct, for example you may add Google's DNS:

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

If you experienced issue when SIP call initiation seems to be delayed, set the nameserver to 127.0.0.1 only and remove other nameservers.

```
nameserver 127.0.0.1
```

Restart network
---------------

```
/etc/init.d/network restart
```

or

```
service network restart
```

Do some checking
----------------

Check IP address:

```
ifconfig
```

Check network route:

```
route -n
```

Ping the gateway and ping Internet:

```
ping 192.168.1.1
ping yahoo.com
```

References
----------

* http://www.centos.org/docs/5/html/Deployment_Guide-en-US/s1-networkscripts-interfaces.html
* http://www.cyberciti.biz/faq/linux-configure-a-static-ip-address-tutorial/
