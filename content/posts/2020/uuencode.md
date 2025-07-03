---
title: "Copying a binary file over a serial console"
date: 2020-07-09T11:48:09-05:00
toc: false
images:
categories:
  - tech
tags: 
  - uuencode
  - uudecode
---

In a previous life, I managed the global anycast DNS infrastructure for a large service provider.  Late one night, a routine maintenance event went south and I ended up locked out of a critical DNS server.   

To make matters more complicated, these systems were protected by strict IP filters on the network interfaces, host based firewalls, and a limited set of installed software.  

The servers were deployed all over the world in several colocation facilities, some staffed by technicians, some not.  

All systems were connected to out-of-band serial consoles and remotely managed power strips in case anything went wrong with the primary network connections or the operating system had a catastrophic failure.

For security reasons, we compiled OpenSSH and manually installed it on all servers.   

During an OS upgrade one evening, SSH crashed and would no longer startup. However, the system was still online and the serial console provided remote access.  

During troubleshooting, we found a shared library was removed during the upgrade:

```
jemurray@serial-console:~$ ldd /usr/sbin/sshd
	linux-vdso.so.1 (0x00007ffea8f65000)
	libwrap.so.0 => /lib/x86_64-linux-gnu/libwrap.so.0 (0x00007f8924b58000)
	libaudit.so.1 => /lib/x86_64-linux-gnu/libaudit.so.1 (0x00007f892492f000)
	libpam.so.0 => /lib/x86_64-linux-gnu/libpam.so.0 (0x00007f8924721000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f89244f9000)
	libsystemd.so.0 => /lib/x86_64-linux-gnu/libsystemd.so.0 (0x00007f8924275000)
	libcrypto.so.1.0.0 => /usr/lib/x86_64-linux-gnu/libcrypto.so.1.0.0 (0x00007f8923e32000)
	libutil.so.1 => /lib/x86_64-linux-gnu/libutil.so.1 (0x00007f8923c2f000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f8923a12000)
	libcrypt.so.1 => /lib/x86_64-linux-gnu/libcrypt.so.1 (0x00007f89237da000)
	libgssapi_krb5.so.2 => /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2 (0x00007f892358f000)
	libkrb5.so.3 => /usr/lib/x86_64-linux-gnu/libkrb5.so.3 (0x00007f89232b9000)
	libcom_err.so.2 => /lib/x86_64-linux-gnu/libcom_err.so.2 (0x00007f89230b5000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f8922cc4000)
	libnsl.so.1 => /lib/x86_64-linux-gnu/libnsl.so.1 (0x00007f8922aaa000)
	libcap-ng.so.0 => NOT FOUND
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f89226a1000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f892242f000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f892502b000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f8922227000)
	liblzma.so.5 => /lib/x86_64-linux-gnu/liblzma.so.5 (0x00007f8922001000)
	liblz4.so.1 => /usr/lib/x86_64-linux-gnu/liblz4.so.1 (0x00007f8921de5000)
	libgcrypt.so.20 => /lib/x86_64-linux-gnu/libgcrypt.so.20 (0x00007f8921ac9000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f89218aa000)
	libk5crypto.so.3 => /usr/lib/x86_64-linux-gnu/libk5crypto.so.3 (0x00007f8921678000)
	libkrb5support.so.0 => /usr/lib/x86_64-linux-gnu/libkrb5support.so.0 (0x00007f892146d000)
	libkeyutils.so.1 => /lib/x86_64-linux-gnu/libkeyutils.so.1 (0x00007f8921269000)
	libresolv.so.2 => /lib/x86_64-linux-gnu/libresolv.so.2 (0x00007f892104e000)
	libgpg-error.so.0 => /lib/x86_64-linux-gnu/libgpg-error.so.0 (0x00007f8920e39000)
```

We were able to locate the missing file on another system:
```
jemurray@another-server:~$ ls -al /lib/x86_64-linux-gnu/libcap-ng.so.0
lrwxrwxrwx 1 root root 18 Sep 24  2017 /lib/x86_64-linux-gnu/libcap-ng.so.0 -> libcap-ng.so.0.0.0


jemurray@another-server:~$ ls -alh /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
-rw-r--r-- 1 root root 19K Sep 24  2017 /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
```

The problem was, how do we get the file to a remote system that only has **serial console** access?  There was no sftp, scp, ftp, or any network-based file transfer utilities.  The systems were stripped down to the bare bones with no extra software to facilitate getting the file onto the broken server.

The system also happened to be in one of the unstaffed facilities.  No way to plug in an external drive.

Serial consoles don't have a file transfer utility.

I had an idea. What if I could cut-and-paste the file to the broken server?   But this is a binary file, that won't work:

```
jemurray@another-server:~$ file /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
/lib/x86_64-linux-gnu/libcap-ng.so.0.0.0: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=7a1459569dc248f22fed84238124d34967d1aff4, stripped

jemurray@another-server:~$ cat /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0 | head -5
@�'*024579:;<2�mt��^:rP�Φ�����AU�葇2�b^�"~�Pl�\����T���qX��|��`Y7?�SBE��=�D��uP��Q�wap��s�����h� ������y��    @
                                                                                                           a�w bp���2����&8 .�R"5�
                                                                                                                                  �&��
                                                                                                                                      `��
                                                                                                                                          �
P1�
2�
  P-�h
      0!�m
          �+b�
              �$�T
                  p+b	HC
                           +G4A '8A <
                                     ��
                                        W4A
                                             p�W
```

Or could it?

`uuencode` will turn a binary file into an ASCII text, with the following command:

```
jemurray@another-server:~$ uuencode /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0 libcap > libcap.txt

jemurray@another-server:~$ more libcap.txt
begin 644 libcap
M?T5,1@(!`0````````````,`/@`!````H!,```````!``````````%A"````
M`````````$``.``(`$``&P`:``$````%````````````````````````````
M````````7#P```````!</```````````(````````0````8```"P/0``````
M`+`](```````L#T@``````"$`P```````(@#```````````@```````"````
M!@```/`]````````\#T@``````#P/2```````.`!````````X`$````````(
M``````````0````$``````(``````````@`````````"````````)```````
M```D``````````0`````````!P````0```"P/0```````+`](```````L#T@
...
```


Confirming the binary file has been converted to ASCII:

```
jemurray@another-server:~$ file libcap.txt
libcap.txt: uuencoded or xxencoded, ASCII text
```

At this point, we are able to cut-and-paste the ASCII text over the serial console into a local file using the terminal and `vi` editor:

```
jemurray@serial-console:~$ vi libcap.txt

begin 644 libcap
M?T5,1@(!`0````````````,`/@`!````H!,```````!``````````%A"````
M`````````$``.``(`$``&P`:``$````%````````````````````````````
M````````7#P```````!</```````````(````````0````8```"P/0``````
M`+`](```````L#T@``````"$`P```````(@#```````````@```````"````
M!@```/`]````````\#T@``````#P/2```````.`!````````X`$````````(
M``````````0````$``````(``````````@`````````"````````)```````
M```D``````````0`````````!P````0```"P/0```````+`](```````L#T@
...
~
~
~
~
~
~
~
~
~
~
~
~
~
~
-- INSERT --                                                  0,1           All
```

The ASCII file is on the broken server, but needs to be converted into the original file with the `uudecode` command:

```
jemurray@serial-console:~$ uudecode -o libcap.img libcap.txt

jemurray@serial-console:~$ file libcap.img
libcap.img: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=7a1459569dc248f22fed84238124d34967d1aff4, stripped
```

Finally, we move the file back to the proper location and fix the permissions:

```
jemurray@serial-console:~$ mv libcap.img /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
jemurray@serial-console:~$ chown root:root /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0 && chmod 644 /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
```

OpenSSH starts and works as expected!


If you are looking for someone capable of coming up with creative solutions to solve your difficult problems, I would be happy to hear from you.