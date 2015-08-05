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

```
ls -l /etc/asterisk/keys
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
