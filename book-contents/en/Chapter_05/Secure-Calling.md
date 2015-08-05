# Secure Calling

## Generate Certificates

```
mkdir -p /etc/asterisk/keys
```
```
cd /home/support/asterisk-11.18.0/contrib/scripts
```

```
./ast_tls_cert -C [COMMON_NAME] -O "[ORG_NAME]" -d /etc/asterisk/keys
```
```
./ast_tls_cert -C 192.168.2.5 -O "My Asterisk" -d /etc/asterisk/keys
```
```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt \
-k /etc/asterisk/keys/ca.key -C [COMMON_NAME] \
-O "[ORG_NAME]" -d /etc/asterisk/keys -o [CLIENT_CERT_NAME]
```

```
./ast_tls_cert -m client -c /etc/asterisk/keys/ca.crt \
-k /etc/asterisk/keys/ca.key -C 192.168.2.5 \
-O "My Asterisk" -d /etc/asterisk/keys -o allphones
```

```
cd /etc/asterisk/keys
```

```
openssl pkcs12 -export -out [CLIENT_CERT_NAME].p12 -inkey ca.key -in ca.crt -certfile asterisk.crt
```

```
openssl pkcs12 -export -out allphones.p12 -inkey ca.key -in ca.crt -certfile asterisk.crt
```

Check generated server and client certificates:

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

What you can distribute to clients:

```
ca.crt
allphones.crt
allphones.p12
``

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
