
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
