# SIP Debug

## Using Asterisk

Enter Asterisk console and run one of these commands to enable SIP debug mode:

```
sip set debug on
sip set debug ip <IP address>
sip set debug peer <extension number or peer name/number>
```

Example:

```
sip set debug ip 192.168.56.77
sip set debug peer 101
sip set debug peer branch-uk
```

Always use tail and tee to watch the log and **catch the error on the spot**:

```
tail -n0 -f /var/log/asterisk/full | tee siplog1.txt
```

## Using ngrep

Many tools we can use to capture SIP traffics, but **ngrep** is the most relatively easy to use.

Install ngrep via apt-get:

```
apt-get install ngrep
```

For CentOS you can search the RPM from [pkgs.org](http://pkgs.org/search/?query=ngrep&type=smart), download and install it:

```
rpm -ivh <ngrep package dot rpm>
```

Use ngrep to capture SIP traffics:

```
ngrep -p -q -W byline -d eth1 port 5060
```

In above example ngrep will capture any activities on port 5060 for both UDP and TCP on device eth1.

We can also save the capture for later reading in siplog1.txt:

```
ngrep -p -q -W byline -d eth1 port 5060 > siplog1.txt
```

This is an example if you need to tap for specific target:

```
ngrep -p -q -W byline udp port 5060 and src 192.168.56.77
```

In above example ngrep will capture any activities on UDP port 5060, in any device and only if they're coming from source IP 192.168.56.77

## SIP requests and responses

Example of SIP REGISTER request caught by ngrep:

```
#
U 192.168.0.141:42350 -> 192.168.0.142:5060
REGISTER sip:192.168.0.142;transport=UDP SIP/2.0.
Via: SIP/2.0/UDP 192.168.0.141:42350;branch=z9hG4bK-d8754z-9ae45c0ce976b12f-1---d8754z-.
Max-Forwards: 70.
Contact: <sip:101@192.168.0.141:42350;rinstance=e9505215f4adaf7e;transport=UDP>.
To: <sip:101@192.168.0.142;transport=UDP>.
From: <sip:101@192.168.0.142;transport=UDP>;tag=462e6f60.
Call-ID: Y2Y1Zjk1MTJkMWM0NjMzZTQxYzEwYzM0NThiNzM0ZTc..
CSeq: 2 REGISTER.
Expires: 60.
Allow: INVITE, ACK, CANCEL, BYE, NOTIFY, REFER, MESSAGE, OPTIONS, INFO, SUBSCRIBE.
Supported: replaces, norefersub, extended-refer, timer, X-cisco-serviceuri.
User-Agent: Zoiper r23496.
Authorization: Digest username="101",realm="asterisk",nonce="49639c93",uri="sip:192.168.0.142;transport=UDP",response="8f8b297596721498a9538da356b05c5e",algorithm=MD5.
Allow-Events: presence, kpml.
Content-Length: 0.
.
```

The response to from Asterisk:

```
#
U 192.168.0.142:5060 -> 192.168.0.141:42350
SIP/2.0 200 OK.
Via: SIP/2.0/UDP 192.168.0.141:42350;branch=z9hG4bK-d8754z-9ae45c0ce976b12f-1---d8754z-;received=192.168.0.141;rport=42350.
From: <sip:101@192.168.0.142;transport=UDP>;tag=462e6f60.
To: <sip:101@192.168.0.142;transport=UDP>;tag=as6da0be67.
Call-ID: Y2Y1Zjk1MTJkMWM0NjMzZTQxYzEwYzM0NThiNzM0ZTc..
CSeq: 2 REGISTER.
Server: Asterisk PBX 11.9.0.
Allow: INVITE, ACK, CANCEL, OPTIONS, BYE, REFER, SUBSCRIBE, NOTIFY, INFO, PUBLISH, MESSAGE.
Supported: replaces, timer.
Expires: 60.
Contact: <sip:101@192.168.0.141:42350;rinstance=e9505215f4adaf7e;transport=UDP>;expires=60.
Date: Wed, 07 May 2014 00:57:31 GMT.
Content-Length: 0.
.
```

Detail information about SIP requests and responses can be found here:
* http://en.wikipedia.org/wiki/List_of_SIP_request_methods
* http://en.wikipedia.org/wiki/List_of_SIP_response_codes
* [SIP Call Flows by Cisco](http://www.cisco.com/c/en/us/td/docs/voice_ip_comm/cuipph/7960g_7940g/sip/7_5/english/administration/guide/ver7_5/sipaxb75.html)