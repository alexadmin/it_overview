```
# check certificate by ip address
openssl s_client  -connect 173.194.71.99:443

# check certificate by domain name
openssl s_client -servername www.google.com  -connect www.google.com:443

# CHECK CERTIFICATE WITH FULL CHAIN
openssl verify -CAfile ca-chain.crt www.alexlinux.com.crt

# REMOVE PASSWORD FROM PRIVATE KEY
openssl rsa -in www.alexlinux.com.key.pem -out www.alexlinux.com.key2.pem

# CHECK CERTIFICATE
openssl x509 -in www.alexlinux.com.crt -text -noout

# CHECK PRIVATE KEY
openssl rsa -in  www.alexlinux.com.key -check

# CHECK ROOT CA AND INTERMEDIATE
openssl verify -CAfile ca.crt intermediate.crt

# APPEND INTERMEDIATE AND CA
cat intermediate.crt ca.crt > chain.crt

# CHECK CERTIFICATE AND PRIVATE KEY
openssl x509 -noout -modulus -in www.alexlinux.com.crt | openssl md5
(stdin)= c75e0ce527eafaddd15f0f77c05e5da9
openssl rsa -noout -modulus -in www.alexlinux.com.key | openssl md5
(stdin)= c75e0ce527eafaddd15f0f77c05e5da9
# BOTH OUTPUT MUST BE EQUAL

# CHECK CERTIFICATE WITH CA
openssl verify -CAfile chain.crt www.alexlinux.com.crt

# ADD LOCAL CERTIFICATE AUTHORITY TO SERVER
update-ca-trust force-enable
cp alexlinux_ca.crt /etc/pki/ca-trust/source/anchors/
update-ca-trust extract

# REMOVE LOCAL CERTIFICATE AUTHORITY TO SERVER
update-ca-trust force-enable
rm /etc/pki/ca-trust/source/anchors/alexlinux_ca.crt
update-ca-trust extract
```

=============================================================

<strong>Generate key and csr in one command</strong>
```
openssl req -new -sha256 -nodes -out wildcard.alexlinux.com.csr -newkey rsa:2048 -keyout wildcard.alexlinux.com.key -config <(
cat <<-EOF
[req]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn
 
[ dn ]
C=RU
ST=Moscow Region
L=Moscow
O=OOO Euroshop
CN = *.alexlinux.com
 
[ req_ext ]
subjectAltName = @alt_names
 
[ alt_names ]
DNS.1 = *.alexlinux.com
DNS.2 = alexlinux.com
EOF
)
```
=============================================================
