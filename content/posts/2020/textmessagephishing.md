---
title: "Following an unsolicited text message link"
date: 2020-09-17T06:34:07-05:00
toc: false
images:
categories:
  - personal
tags: 
  - spam
  - phishing
  - textmessage
---

# Intro

I rarely receive unsolicited text messages.  Starting in August 2020 that changed.  There are now between 2 and 3 messages a week.   They all revolve around 'lost packages', 'porn', and 'work from home'.

**NEVER** click a unsolicited link sent via text or email.  There is a good chance it is spam, phishing, or some type of malware.  Clicking these links lets the sender know the phone number or email address is valid and someone is reading what they are sending.  This is a sure fire way to get your phone number added to even more campaigns.  

But, what happens when you click the link?   Let's find out...


# Example Text Messages

Here is a small example of the latest messages:

```
Voicemail: Parcel Sorting Center: Found package addressed to Jason. Last pick-up day - Friday!  n4scn.info/Ig8rnM9yadfUidjsY
```

```
Jason, we found a parcel from March pending for you. Kindly assume ownership and confirm for delivery here: m8svk.info/rqJ0shwlahdfQLqLPe
```

```
Jason, urgent alert about your USPS package 8L96K5 from 03/06/2020. Proceed to m7smz.info/ShA7p5kGc
```

```
hey is this Jason? Are you still single? Im bored lol. Check out my pics here and if you like what you see lets meet up xoxo Becky 
muaydtin.com/as85YQluxslxkdhsogCe
```

```
Jason, final notice about your USPS package 5K33892l8S6 from 03/10/2020. Proceed to m3svc.info/4pGS9oiahskwlqGE3
```

```
hey Jason , do you remember me lol? we chatted before, im very bored and lonely hehe. cum chat w/ me. check out my new pix

 http://m337u.com/SPrtqygkskwlskkihhzWD
```

```
Pornstar Abella Danger said the number 1 reason she's attracted to a guy - lasting long enough to pleasure her. Jason, satisfy her: l7smv.info/rDaggsldhwlsMeNOX
```

```
Jason, we found a parcel from June pending for you. Kindly claim ownership and schedule for delivery here: l2scr.info/KiLMlllsLslwhlbbsns
```

```
Jason this is Dan, you were recommended for our remote work from home position.

Pay: $499/day

Required: PC or Smartphone

http://homew1.xyz/284yalSslexlEBJyaKE
```

# Clicking on a link

**Warning:** I would not recommend ever clicking one of these links!  Your asking to get your number added to even more spam and phishing messages.  I will be your sacrificial lamb and click the link.


After clicking on the link first, we are redirected to:

```
<meta http-equiv='refresh' content='0;http://go.slightsp.com/ts5603-sms-del-us?clickid=5f627a2df417a600018ab5d7'>
```

Where we finally end up at:

```
https://messageinbox.club/link/?trk=trackingforhronly.link&city=&uclick=16a74k&uclickhash=16a74k-16a74k-pm-pm-tw-178n-du-170b25
```

Which looks like this:

![page load sources](/images/textscam1.png)


The page source is not viewable.  It pops up a 403 error:

```
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access /link/
on this server.</p>
</body></html>
```

Switching over to the `inspect` engine in Chrome yields much more information about the page: 

![page load sources](/images/textscam4.png)

Of course I will try the survey.  Free stuff is good:

![page load sources](/images/textscam2.png)



Let's click the survey and see where it takes us:

![page load sources](/images/textscam3.png)


CBD, Pills, and Watches:

![page load sources](/images/textscam5.png)

What if we try to order one:

![page load sources](/images/textscam6.png)

My bogus credit card fails:

![page load sources](/images/textscam7.png)


Welcome to the world of the profession pill, watch, and scam sales person.   Hopefully you enjoyed the tour.  I would go farther, but it looks like a sales scam.  No obvious signs of malware or other hacking tools.

