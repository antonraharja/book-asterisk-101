Secure Calling
==============

Secure calling can be achieved by enabling **TLS**. Once implemented SIP UA can choose to use transport TLS instead of UDP or TCP. The advantage of choosing TLS is that the SIP traffic exchanged between SIP UA and Asterisk will be encrypted, it means it will take a considerable amount of time and effort to decrypt it without the encryption key, if not possible.

There are 3 sub-topics in this topic, they are:

- Secure SIP
- Secure RTP
- ZRTP

# Secure SIP

SIP is the signalling part of communication.

## Generate Certificates

Create placeholder for our keys:

```
mkdir -p /etc/asterisk/keys
```

Navigate to Asterisk source code directory and its `contrib/scripts`:

```
cd /home/support/asterisk-11.18.0/contrib/scripts
```

Use contributed script to generate self-signed certificates.

Usage for server certificate:

```
./ast_tls_cert -C [COMMON_NAME] -O "[ORG_NAME]" -d /etc/asterisk/keys
```

Example of generating server certificate:

```
./ast_tls_cert -C 192.168.2.5 -O "My Asterisk" -d /etc/asterisk/keys
```

Usage for client certificate:

```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt -k /etc/asterisk/keys/ca.key -C [COMMON_NAME] -O "[ORG_NAME]" -d /etc/asterisk/keys -o [CLIENT_CERT_NAME]
```

Example of generating client certificate:

```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt -k /etc/asterisk/keys/ca.key -C 192.168.2.5 -O "My Asterisk" -d /etc/asterisk/keys -o allphones
```

Navigate to our keys placeholder:

```
cd /etc/asterisk/keys
```

Generate .p12 file for clients requesting this format, usage:

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

You should found similar to this:

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

What you can distribute to clients and they can use on their SIP UA:

```
ca.crt
asterisk.crt
allphones.crt
allphones.p12
```

## Enable TLS supports

Edit `/etc/asterisk/sip.conf` and add below options. In FreePBX you will need to add these into `/etc/asterisk/sip_general_custom.conf`

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

Note: In FreePBX you can enable this from Extensions menu and the apply configuration.

# Secure RTP

RTP is the media path or data part of communication. This the actual voice stream that need to be protected.

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

In FreePBX you can enable this from Extensions menu and then apply configuration.
