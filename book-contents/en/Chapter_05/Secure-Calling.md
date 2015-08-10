Secure Calling
==============

Secure calling can be achieved by enabling **TLS** to encrypt the signalling and enabling **SRTP** or **ZRTP** to encrypt the media or data. Once implemented SIP UA can choose to use transport TLS instead of UDP or TCP. The advantage of choosing TLS is that the SIP traffic exchanged between SIP UA and Asterisk will be encrypted, it means it will take a considerable amount of time and effort to decrypt it without the encryption key, if not possible.

There are 3 sub-topics in this topic, they are:

- [Secure SIP](#secure-sip)
- [Secure RTP](#secure-rtp)
- [ZRTP](#zrtp)

Later after going through this topic in the end you must decide whether you want to implement TLS with SRTP or TLS with ZRTP. You cannot have both for a call, but you might have both implemented on your systems (for different calls).

Please note that having TLS enabled is a must, and successfully configure and enable SRTP or ZRTP is crucial on any Asterisk deployment.

# Secure SIP

SIP is the signalling part of communication. Securing SIP means that we are protecting the call metadata from the Man in The Middle.

## Generate Certificates

Create placeholder for our keys:

```
mkdir -p /etc/asterisk/keys
```

Navigate to Asterisk source code directory and its `contrib/scripts`. Example:

```
cd /home/support/asterisk-11.18.0/contrib/scripts
```

Use contributed script to generate self-signed certificates. Usage for server certificate:

```
./ast_tls_cert -C [COMMON_NAME] -O "[ORG_NAME]" -d /etc/asterisk/keys
```

Example of generating server certificate:

```
./ast_tls_cert -C 192.168.2.5 -O "My Asterisk" -d /etc/asterisk/keys
```

Usage for client certificate:

```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt -k /etc/asterisk/keys/ca.key -C [CLIENT_IP_HOSTNAME] -O "[ORG_NAME]" -d /etc/asterisk/keys -o [CLIENT_CERT_NAME]
```

Example of generating client certificate:

```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt -k /etc/asterisk/keys/ca.key -C 192.168.2.10 -O "My Asterisk" -d /etc/asterisk/keys -o allphones
```

You may omit the `-C [CLIENT_IP_HOSTNAME]` parameter if you intend to create the client certificate for all SIP UA.

Navigate to our keys placeholder again:

```
cd /etc/asterisk/keys
```

Generate `.p12` file for SIP UA requesting certificate in this format, usage:

```
openssl pkcs12 -export -out [CLIENT_CERT_NAME].p12 -inkey ca.key -in ca.crt -certfile asterisk.crt
```

Example:

```
openssl pkcs12 -export -out allphones.p12 -inkey ca.key -in ca.crt -certfile asterisk.crt
```

Check all generated server and client certificates:

```
ls -l /etc/asterisk/keys
```

You should found similar list to this:

```
-rw------- 1 root root 1208 Aug  5 09:56 allphones.crt
-rw------- 1 root root  570 Aug  5 09:56 allphones.csr
-rw------- 1 root root  887 Aug  5 09:56 allphones.key
-rw-r--r-- 1 root root 4909 Aug  5 09:57 allphones.p12
-rw------- 1 root root 2095 Aug  5 09:56 allphones.pem
-rw------- 1 root root 1208 Aug  5 09:53 asterisk.crt
-rw------- 1 root root  570 Aug  5 09:53 asterisk.csr
-rw------- 1 root root  887 Aug  5 09:53 asterisk.key
-rw------- 1 root root 2095 Aug  5 09:53 asterisk.pem
-rw------- 1 root root  157 Aug  5 09:53 ca.cfg
-rw------- 1 root root 1757 Aug  5 09:53 ca.crt
-rw------- 1 root root 3311 Aug  5 09:53 ca.key
-rw------- 1 root root  118 Aug  5 09:56 tmp.cfg
```

Here are files that you can distribute to clients where they can use them on their SIP UA:

```
ca.crt
asterisk.crt
allphones.crt
allphones.p12
```

## Enable TLS supports

Edit `/etc/asterisk/sip.conf` and add below options:

```
tlsenable=yes
tlsbindaddr=0.0.0.0
tlscertfile=/etc/asterisk/keys/asterisk.pem
tlscafile=/etc/asterisk/keys/ca.crt
tlscipher=ALL
tlsclientmethod=tlsv1
```

Followed by Asterisk restart

```
asterisk -rx 'core restart now'
```

## Verify Configuration

Look for port 5061 in netstat result:

```
netstat -lnptu | grep 5061
```

Enable `transport` option on each SIP accounts.

Edit `/etc/asterisk/sip_friends.conf`:

```
[101]
type=friend
...
...
transport=tls
```

Reload SIP configuration:

```
asterisk -rx 'sip reload'
```

## SIP UA configuration

SIP UA need to use the supplied client certificate by adding it though the provided menu on SIP UA and then add the CA certificate to their computer if the CA certificate is a self-signed certificate.

Adding CA certificate to Ubuntu (as root):

```
cp ca.crt /usr/local/share/ca-certificates/asterisk-server.crt
update-ca-certificates
```

Adding CA certificate to CentOS (as root):

```
yum install ca-certificates
update-ca-trust enable
cp ca.crt /etc/pki/ca-trust/source/anchors/
update-ca-trust extract
```

# Secure RTP

RTP is the media path or data part of communication. This is the actual voice stream that need to be protected. An encrypted RTP packets will not be easy (read: virtually impossible) to listen to.

## Enable encryption

Enable RTP encryption for specific SIP account by turning `encryption` option to `yes` in SIP account. The `transport` option must be `tls` and SIP UA must be registered and using transport TLS.

Edit `/etc/asterisk/sip_friends.conf`:

```
[101]
type=friend
...
...
transport=tls
encryption=yes
```

Reload SIP configuration:

```
asterisk -rx 'sip reload'
```

# ZRTP

Quoting Wikipedia:

> ZRTP (composed of Z and Real-time Transport Protocol) is a cryptographic key-agreement protocol
> to negotiate the keys for encryption between two end points in a Voice over Internet Protocol (VoIP)
> phone telephony call based on the Real-time Transport Protocol. It uses Diffie–Hellman key exchange
> and the Secure Real-time Transport Protocol (SRTP) for encryption.

What I know about **Asterisk+ZRTP**:

- ZRTP is using Secure RTP to encrypt data
- ZRTP implements Short Authentication String (SAS) to mitigate Man in The Middle attack (basically this is to make sure that the other end is someone the caller knows verbally)
- ZRTP initiated by the caller and the callee must respond to it
- We need to choose between using **TLS+SRTP** or **TLS+ZRTP**, cannot have both in one secure call
- Asterisk do not understand ZRTP and cannot allow ZRTP to pass between participating SIP accounts, therefore the RTP (which is actually ZRTP packets) between SIP accounts must be direct peer-to-peer without Asterisk intervention
- No NAT between each participating SIP accounts in conversation

Please note: You do not need to setup Asterisk for ZRTP if you already choose to use TLS with SRTP. Further more setting up Asterisk for ZRTP is currently limiting your Asterisk to provide some feature, or should I say you will be forced to limit some feature otherwise ZRTP won't work.

Continue ? Ok.
 
Direct peer-to-peer RTP between SIP accounts in Asterisk requires certain condition to be met. On each SIP accounts you must set the following:

- Disable NAT support by setting `nat` to `no` on each peers
- Disable SRTP by setting `encryption` to `no` on each peers, you can still use `transport=tls`
- Enable peer-to-peer media path by setting `directmedia` to `yes` on each peers
- Set `directrtpsetup` to `yes` globally
- Set disallowed and allowed codec the same for all participating SIP accounts, for example by setting `disallow=all` followed by `allow=ulaw`

Below is the example of ZRTP preparation for a SIP account (in this case `101`). You must do these type of changes on all participating SIP accounts.

Edit `/etc/asterisk/sip_friends.conf`:

```
[101]
type=friend
...
...
transport=tls
encryption=no
nat=no
directmedia=yes
disallow=all
allow=ulaw
```

Edit `/etc/asterisk/sip.conf`, add option `directrtpsetup=yes` just above the include of file `sip_friends.conf`:

```
...
...
directrtpsetup=yes
...
...
#include "sip_friends.conf"
```

Reload SIP configuration:

```
asterisk -rx 'sip reload'
```

Continue to configure all participating SIP UA to disable SRTP and enable or create ZRTP session.

# References

- [Secure Calling Tutorial](https://wiki.asterisk.org/wiki/display/AST/Secure+Calling+Tutorial)
