Create Self-Signed SSL Certificates
---

#### 1. Create a rootCA.heylinux.com.pem

```bash
# The rootCA.heylinux.com.pem will be used to generate the server key and the trusted root certification authorities of browsers such as Chrome
# The -subj parameter is more straightforward than the interactive steps

$ openssl genrsa -out rootCA.heylinux.com.key 4096
$ openssl req -x509 -new -nodes -key rootCA.heylinux.com.key -sha256 -days 3650 -out rootCA.heylinux.com.pem -subj "/C=CN/ST=Sichuan/L=Chengdu/O=HEYLINUX/OU=IT/CN=SRE"
```

#### 2. Create a ssl.conf for basic key parameters

```bash
# The ssl.conf will be used to generate a server key
# To support the Chrome browser, the "subjectAltName" is required instead of "commonName"

$ vim ssl.conf
subjectAltName = @alt_names
authorityKeyIdentifier = keyid,issuer
keyUsage = digitalSignature,keyEncipherment,nonRepudiation,dataEncipherment
extendedKeyUsage = serverAuth,clientAuth
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash

[alt_names]
DNS.1 = *.heylinux.com
DNS.2 = *.cloud.heylinux.com
```

#### 3. Create a private key as star.heylinux.com.key and a server key as star.heylinux.com.crt with rootCA.heylinux.com.pem

```bash
# The star.heylinux.com.key and star.heylinux.com.crt could be used for servers such as Apache, Nginx
# And generate the other bundle certificates based on them
# The -subj parameter is more straightforward than the interactive steps

$ openssl req -new -nodes -out star.heylinux.com.csr -newkey rsa:4096 -keyout star.heylinux.com.key -subj "/C=CN/ST=Sichuan/L=Chengdu/O=HEYLINUX/OU=IT/CN=*.heylinux.com"
$ openssl x509 -req -in star.heylinux.com.csr -CA rootCA.heylinux.com.pem -CAkey rootCA.heylinux.com.key -CAcreateserial -out star.heylinux.com.crt -days 3650 -sha256 -extfile ssl.conf
```

#### 4. Review the server key

```bash
$ openssl x509 -text -noout -in star.heylinux.com.crt
```


#### 5. Create bundle certificates which including the server key and private key

```bash
# Create a certificate bundle as PKCS12 with a passphrase: "P_Ss0rdT"
# The star.heylinux.com.p12 could be used for servers such as Tomcat
$ openssl pkcs12 -export -in star.heylinux.com.csr -inkey star.heylinux.com.key -password pass:P_Ss0rdT -name heylinux_com -out star.heylinux.com.p12

# Convert a certificate bundle from PKCS12 to JKS with a passphrase: "P_Ss0rdT"
# The star.heylinux.com.jks could be used for servers such as Tomcat
$ keytool -importkeystore -deststorepass P_Ss0rdT -destkeystore star.heylinux.com.jks -srcstorepass P_Ss0rdT -srckeystore star.heylinux.com.p12 -srcstoretype PKCS12

# Convert a certificate bundle from PKCS12 to PEM without passphrase
# The star.heylinux.com.pem could be used for servers such as Apache, Nginx, HAProxy and AWS ELB
$ openssl pkcs12 -password pass:P_Ss0rdT -in star.heylinux.com.p12 -out star.heylinux.com.pem -nodes

# View all ssl certificates in a bundle star.heylinux.com.pem
# Each server key and private key will be displayed as a pair with the given alias name such as "heylinux_com"
$ openssl crl2pkcs7 -nocrl -certfile star.heylinux.com.pem | openssl pkcs7 -print_certs -text -noout
```

#### 6. Create a truststore JKS file from rootCA.heylinux.com.pem with a passphrase: "P_Ss0rdT"

```bash
# Create a truststore JKS file as the rootCA for servers such as Tomcat

$ openssl x509 -in rootCA.heylinux.com.pem -out rootCA.heylinux.com.der -outform der
$ keytool -v -printcert -file rootCA.heylinux.com.der
$ keytool -importcert -alias heylinux_com -keystore rootCA.heylinux.com.jks -storepass P_Ss0rdT -file rootCA.heylinux.com.der
$ keytool -keystore rootCA.heylinux.com.jks -storepass P_Ss0rdT -list
```

#### 7. Generate heylinux-ssl-keypair for SSL keypair authentication with a passphrase: "P_Ss0rdT"

```bash
# Generate the SSL keypair for authentication, eg: between NiFi client and server
$ openssl req -x509 -newkey rsa -keyout heylinux-ssl-keypair.key -out heylinux-ssl-keypair.crt -days 3650 -subj "/C=CN/ST=Sichuan/L=Chengdu/O=HEYLINUX/OU=IT/CN=SRE"
Generating a RSA private key
....................................+++++
writing new private key to 'heylinux-ssl-keypair.key'
Enter PEM pass phrase: P_Ss0rdT
Verifying - Enter PEM pass phrase: P_Ss0rdT

# Convert a certificate bundle from .key and .crt to PKCS12
$ openssl pkcs12 -export -in heylinux-ssl-keypair.crt -inkey heylinux-ssl-keypair.key -password pass:P_Ss0rdT -name heylinux_ssl_keypair -out heylinux-ssl-keypair.p12
Enter pass phrase for heylinux-ssl-keypair.key: P_Ss0rdT

# Verify the PKCS12 file content
$ keytool -list -v -keystore heylinux-ssl-keypair.p12 -storepass P_Ss0rdT -storetype PKCS12
```
