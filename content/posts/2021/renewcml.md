---
title: "Renewing a CML license"
date: 2021-04-12T06:43:45-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - cml
  - licensee
description: Instructions to renew the Cisco Modeling Labs yearly subscription license.
---

In March 2021, my [Cisco Modeling Lab (CML)](https://learningnetworkstore.cisco.com/cisco-modeling-labs-personal/cisco-cml-personal) license expired. Last year I purchased the standard 20 node license. However, with 64 CPUs and 1T of memory my lab has horsepower to spare. I upgraded to the 40 node license this year. To move between license a few additional steps are required.

Enter the `Tools -> Licensing` menu:

[![Image of cisco cml](/images/2021-04-10-15-44-28.png)](/images/2021-04-10-15-44-28.png)

I am not 100% sure this step is necessary, but I `deregistered` the product first since a new license was generated:

[![Image of cisco cml](/images/2021-04-10-15-44-49.png)](/images/2021-04-10-15-44-49.png)

Deregister:

[![Image of cisco cml](/images/2021-04-10-15-45-04.png)](/images/2021-04-10-15-45-04.png)

After removing the license, the system automatically enters `evaluation` mode:

[![Image of cisco cml](/images/2021-04-10-15-45-33.png)](/images/2021-04-10-15-45-33.png)

To switch from the 20 to 40 node licenses, select `Product Configuration -> Edit` and select the `CML Plus` license:

[![Image of cisco cml](/images/2021-04-10-16-17-13.png)](/images/2021-04-10-16-17-13.png)

Go to the Cisco CML account webpage, select the product, then copy the license text from the 2.x license button:

[![Image of cisco cml](/images/2021-04-10-15-46-03.png)](/images/2021-04-10-15-46-03.png)

Paste the license text and select Register:

[![Image of cisco cml](/images/2021-04-10-15-46-35.png)](/images/2021-04-10-15-46-35.png)

The system is licensed:

[![Image of cisco cml](/images/2021-04-10-16-18-05.png)](/images/2021-04-10-16-18-05.png)