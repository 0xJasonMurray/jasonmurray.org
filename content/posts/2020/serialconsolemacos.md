---
title: "Accessing a serial console via the macOS terminal"
date: 2020-09-06T14:17:16-05:00
toc: false
images:
categories:
  - tech
tags: 
  - serial
  - cu
  - usbserial
  - usb
  - console
---

## Quick Start

The command `cu` can be used to connect to a serial console on macOS (and most any other unix based operating systems).  There are lots of other commands that will work as serial consoles, but this command comes installed by default and is easy to use.   In fact many unix based systems have `cu` installed by default.  Don't bother with other tools, `cu` is lightweight and works well.

The default options will connect to a 9600bps serial console:

```
sudo cu -l /dev/tty.usbserial
```

Set the speed option to connect to 115,200bps consoles:

```
sudo cu -s 115200 -l /dev/tty.usbserial
```


## Details

Plug in the serial cable.  Verify the serial driver is loaded by running the following command.   There should be a `/dev/tty.usbserial` file if everything is working as expected:

```
jemurray@mbp-2019:~ $ ls -al /dev/tty.usbserial
crw-rw-rw-  1 root  wheel    9,   8 Sep  6 14:21 /dev/tty.usbserial
```

If this is the first time using a new USB serial cable, you probably didn't see anything after running the command above.  Most USB serial cables are based on the [PL2303](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41) serial chip set.  On macOS this serial driver is not installed by default.  

Download and install the driver from here: [http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41).

Run the: `ls -al /dev/tty.usbserial` again.  You should see the output displayed in the first step.  If not, troubleshoot the issue until a USB serial driver is loaded properly.



For serial consoles, the most common command line options to `cu` are:

- `-l`: USB serial device.  On macOS this is typically /dev/tty.usbserial (on Linux /dev/ttyUSB0 or something in the /dev directory with USB in the name).
- `-s`: USB serial console speed.  Most consoles are either 9600 (the default) or 115200.   If you connect to a console and see nothing or gibberish characters try changing the speed first.


Connect to a console using the default settings:

```
sudo cu -l /dev/tty.usbserial
Password:
Connected.

cisco-sw-home con0 is now available

Press RETURN to get started.

***********************************************************************
*        WARNING - Unauthorized access to or use of this system is    *
*                         strictly prohibited.                        *
*  Use of this system is subject to all policies and procedures set   *
*  forth by the Example Company located at                            *
*  https://example.com/about/compliance-policies. Unauthorized use is *
*  prohibited and may result in administrative or legal action. The   *
*  Example Company may monitor the use of this system.                *
***********************************************************************


User Access Verification

Username:
```

Once connected, use the `~` (escape sequence) to send options to a running `cu` instance.  For example, type `<enter>~?` (most of the time it is necessary to press `<enter>` before pressing `~`) to bring up the `cu` menu:

```
~?
[Escape sequences]
[~. hangup]                   [~!CMD run shell]
[~$CMD stdout to remote]      [~|CMD stdin from remote]
[~+CMD stdin and stdout to remote]
[~# send break]               [~cDIR change directory]
[~> send file]                [~< receive file]
[~pFROM TO send to Unix]      [~tFROM TO receive from Unix]
[~sVAR VAL set variable]      [~sVAR set boolean]
[~s!VAR unset boolean]        [~v list variables]
[~z suspend]
[~%break send break]         [~%cd DIR change directory]
[~%put FROM TO send file]    [~%take FROM TO receive file]
[~%nostop no XON/XOFF]       [~%stop use XON/XOFF]
```

The most common serial commands are:

- `<enter>~%`: To send-break
- `<enter>~.`: Quit `cu`



