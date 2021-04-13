---
layout: post
title:  "Part 2 on how does the Network tab work in explorer"
date:   2020-06-18 19:49:00 -0700
categories: windows_research
tags: windows_research
---

Today I want to find out why and what causes the **BROWSER** packets to broadcast.

First I decided to change my desktop's hostname. It sent an NBNS packet:

![image](/assets/images/nbns-packet-changehostname2.png)

Then we see another packet being sent, a IGMPv3, we should research what this one is in another post.

![image](/assets/images/igmpv3-packet-changehostname.png)

After after, we see that it sent a new BROWSER packet! With the new HOSTNAME!

![image](/assets/images/browser-packet-changehostname.png)

So I decided to keep playing with the *BROWSER* packets, the seem interesting, don't they ;)

So I rebooted my PC after changing the hostname and wooo, I was surprised how many packets it sent

First we see some IGMPv3 packets, as I said, we will research what those are in another post :D

![image](/assets/images/igmpv3-packet2-boot-changehostname.png)

Next we seem to see some MDNS queries, let's also ignore those

![image](/assets/images/mdns-packet-boot-changehostname.png)

Then we see again the well known NBNS packets

![image](/assets/images/nbns-packet2-boot-changehostname.png)

And more NBNS packets..........

![image](/assets/images/more-nbns-packets-change-hostname.png)

But I didn't see any browser packet :(. I might missed them, shrug.

Let's do another reboot. This time without doing any change, and guess what! Yup, we got a browser packet after pressing the reboot button!

![image](/assets/images/
browser-reboot1.png)

And another one at boot....

![image](/assets/images/
browser-reboot2.png)

But seems like my laptop also sent a browser packet, hmm, so that means those browser packets are being sent by Windows devices regularly?

![image](/assets/images/browser-from-laptop.png)

I decided to do some normal stuff on my computer (Check Discord, play a game, code) while leaving my laptop monitor browser packets.

After 10 minutes of leaving my computer idling.
It sent 1 browser packet:

![image](/assets/images/browser-frompc-1min.png)

So I let it more AFK...

![image](/assets/images/browser-frompc-1min2.png)

More browser packets went to my laptop, and with simple math we found out that they are being sent (probably) every 10 minutes!

In part 3 we will be making a tool to sniff those packets!