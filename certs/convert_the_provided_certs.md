Convert the provided certs to required formats
---

#### 1. Get the certs from Windows Certificate Authority

```bash
$ ls -1
star.heylinux.com.cer
star.heylinux.com.pfx
SubCA_2.p7b
```

#### 2. Convert the P7B(P7) to PEM and JKS

```bash
$ openssl pkcs7 -inform DER -print_certs -in SubCA_2.p7b -out rootCA.heylinux.com.pem
$ penssl x509 -in rootCA.heylinux.com.pem -out rootCA.heylinux.com.der -outform der
$ keytool -v -printcert -file rootCA.heylinux.com.der

$ keytool -importcert -alias heylinux_com -keystore rootCA.heylinux.com.jks -storepass P_Ss0rdT -file rootCA.heylinux.com.der
Trust this certificate? [no]:  yes
Certificate was added to keystore
```

#### 3. Convert the PFX(P12) to PEM and copy the PRIVATE KEY and SERVER CERT to .key and .crt files

```bash
$ openssl pkcs12 -password pass:P_Ss0rdT -nodes -in star.heylinux.com.pfx -out star.heylinux.com.pem

$ cp star.heylinux.com.pem star.heylinux.com.key
$ cp star.heylinux.com.pem star.heylinux.com.crt

# Update the files to only keep the PRIVATE block in .key and the CERTIFICATE blocks in .crt
$ vim star.heylinux.com.key
$ vim star.heylinux.com.crt
```

#### 4. Create the P12 and JKS with alias heylinux_com based on the .key and .crt files

```bash
$ openssl pkcs12 -export -in star.heylinux.com.crt -inkey star.heylinux.com.key -password pass:P_Ss0rdT -name heylinux_com -out star.heylinux.com.p12
$ keytool -importkeystore -deststorepass P_Ss0rdT -destkeystore star.heylinux.com.jks -srcstorepass P_Ss0rdT -srckeystore star.heylinux.com.p12 -srcstoretype PKCS12 -alias heylinux_com
```

#### 5. List the certs

```bash
$ ls -1
rootCA.heylinux.com.der
rootCA.heylinux.com.jks
rootCA.heylinux.com.pem
star.heylinux.com.cer
star.heylinux.com.crt
star.heylinux.com.jks
star.heylinux.com.key
star.heylinux.com.p12
star.heylinux.com.pem
star.heylinux.com.pfx
SubCA_2.p7b
```

#### 6. Verify the certs

```bash
$ openssl crl2pkcs7 -nocrl -certfile rootCA.heylinux.com.pem | openssl pkcs7 -print_certs -text -noout
$ openssl crl2pkcs7 -nocrl -certfile star.heylinux.com.pem | openssl pkcs7 -print_certs -text -noout
$ keytool -list -v -keystore star.heylinux.com.p12 -storepass P_Ss0rdT -storetype PKCS12
$ keytool -list -v -keystore star.heylinux.com.jks -storepass P_Ss0rdT
```
