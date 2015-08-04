# Setting IP Address in Ubuntu

Edit /etc/network/interfaces
----------------------------

Example of ```/etc/network/interfaces```:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.1.12
    netmask 255.255.255.0
    gateway 192.168.1.1

auto eth1
iface eth1 inet static
    address 192.168.2.12
    netmask 255.255.255.0
```

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
service networking restart
```

or in some distro:

```
/etc/init.d/networking restart
```

or preferable way on Ubuntu server 14.04 and above:

```
ifdown eth0 && ifup eth0
ifdown eth1 && ifup eth1
```

Do some checking
----------------

Check IP address:

```
ifconfig
ifconfig eth0
ip addr
ip addr show dev eth0
```

Check network route:

```
route -n
ip route
```

Ping the gateway and ping Internet:

```
ping 192.168.1.1
ping yahoo.com
```
