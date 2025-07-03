---
title: "Zeek Week 2020 - CTF sudo su writeup"
date: 2020-10-19T06:00:35-05:00
toc: false
images:
categories:
  - tech
tags: 
  - zeekweek
  - ctf
---

[Yesterday I posted about my first ZeekWeek Capture the Flag challenge](/posts/2020/zeekweekmanormachine/).  Today we walk through the details to solve the second CFT challenge.

Using a packet capture from an `ssh` session, determine the length of the password entered on the command line.  The following details were given:

- The user SSHed into a system
- Public keys performed the initial authentication (ie. no password)
- The user typed: `sudo su` + `<their password>` + `^d^d`

With this information, I set out to reproduce the same scenario.  I would `ssh` into my lab server and reproduce exactly what the CTF challenge did. Then, compare my pattern of packets to their pattern of packets.  I presumed there should be obvious similarities between the two captures.  If you have not reviewed [yesterdays CTF](/posts/2020/zeekweekmanormachine/), now is a good time.  I talk about using changes in patterns to solve puzzles like these.  

I made the following assumptions:

- Key presses on the console generate a single packet
- The packets should be the same size in my test and the ZeekWeek packet captures
- There should be an obvious timing interval between key presses
- WireShark will produce a graph to help me find the pattern

I first tested the theory of a timing interval between key presses.  By using the WireShark `I/O graph` set to 1ms intervals, an obvious pattern appears.   Each blip on the line is probably a key press:

The side-by-side comparison of capture files confirms my assumption:

[![Wireshark pattern](/images/zeekweeksudosu-1.png)](/images/zeekweeksudosu-1.png)


Based on the above timing intervals, I am guessing the password is 6 characters.  There are only 2 attempts to solve this challenge. More testing is required.

Next, I set out to confirm my theory behind key presses.  I presume that key-presses should always be the same size.  An obvious pattern that popped up was a frame length of 102 bytes.   Let's filter for clients who are sending 102 byte frames:

```
tcp.srcport == 52840 && frame.len == 102
```

The password I typed in my example session was 9 characters, my assumption is theirs is 6.  To confirm my theory, there should be a difference of 3 102 byte frames between the two packet captures?   Yes, there is. Mine has 21 their has 18:

[![Wireshark pattern](/images/zeekweeksudosu-2.png)](/images/zeekweeksudosu-2.png)


Solved.  The password length is 6 characters.
