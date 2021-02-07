---
title: "Using OpenSSL to create and validate self-signed certificate in one command"
date: 2020-11-07T15:00:51-06:00
toc: false
images:
categories:
  - tech
tags: 
  - openssl
  - ssl
  - selfsigned
  - certificate
---

## Single command with all options and no password (fully automated)

Command:

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes -subj "/C=US/ST=Missouuri/L=St. Louuis/O=Company Name/OU=Org/CN=www.example.com"
```

Full output: 

```
jemurray@mbp-2019:~/test $ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes -subj "/C=US/ST=Missouuri/L=St. Louuis/O=Company Name/OU=Org/CN=www.example.com"
Generating a 4096 bit RSA private key
...................................................++
...................................++
writing new private key to 'key.pem'
-----
```

## Single command with password and manual information entry

Command:

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
```

Full Output:

```
jemurray@mbp-2019:~/test $ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
Generating a 4096 bit RSA private key
....................................................................++
.............................................................................................++
writing new private key to 'key.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:US
State or Province Name (full name) []:Missouri
Locality Name (eg, city) []:St. Louis
Organization Name (eg, company) []:My Company
Organizational Unit Name (eg, section) []:My Org
Common Name (eg, fully qualified host name) []:www.example.com
Email Address []:none@example.com
```

## View and validate the certificate

Command:

```
openssl x509 -in cert.pem -text -noout
```

Full output:

```
jemurray@mbp-2019:~/test $ openssl x509 -in cert.pem -text -noout
Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number: 15857457675749212053 (0xdc1101bd5bd2bf95)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, ST=Missouuri, L=St. Louuis, O=Company Name, OU=Org, CN=www.example.com
        Validity
            Not Before: Nov  7 21:08:36 2020 GMT
            Not After : Nov  7 21:08:36 2021 GMT
        Subject: C=US, ST=Missouuri, L=St. Louuis, O=Company Name, OU=Org, CN=www.example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:f6:65:a2:e4:b1:1e:56:88:c0:d6:b8:17:7c:ef:
                    7b:d4:63:55:1e:a0:10:91:cb:13:b7:37:e1:49:16:
                    4d:a0:99:98:d5:3f:c3:52:83:e0:ad:ba:a8:46:e0:
                    0f:76:83:6d:8c:3e:54:c6:87:4f:21:8a:7e:6c:9a:
```