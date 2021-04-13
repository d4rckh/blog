---
layout: post
title:  "Part 1 on how does the Network tab work in explorer"
date:   2020-06-18 19:24:55 -0700
categories: windows_research
tags: windows_research
---

I love researching Windows protocols, and that's how you can do it too:
- Do stuff
- Check Wireshark for packets
- Google

In this mini-series I will be showing you how I found out about the Microsoft Windows Browser Protocol, and how I made my own simple script using _pyshark_ to sniff those!

First of all, I started playing with the **Network** tab in explorer.

![image](/assets/images/network-explorer.jpg)

Of course, I was wondering, where does Windows know which computers are on my network, so I started a wireshark capture and refreshed the tab

![image](/assets/images/network-explorer-refresh.png)

Looking at my wireshark capture I can clearly see that it broadcasts few packets:

![image](/assets/images/network-explorer-nbns-packets.png)
We can clearly see that it asks *who is in Workgroup?*

But, I was not seeing any response from my Computer, so I let my Laptop idle.
After a hour, I checked wireshark and filtered out packets coming from **my computer** (`ip.src = 192.168.0.XXX`) to my laptop, and to my surprise, there were some cool packets, one of them was **BROWSER**.

![image](/assets/images/network-explorer-browser-packet.png)

So I inspected it. And too be honest, I was surprised how juicy it is.

![image](/assets/images/network-explorer-browser-fields.png)

But what made me interested was the `Host Comment` field. What is it? Oh, I remember! It's my Computer's description!

So I went, and changed it.

![image](/assets/images/network-explorer-computer-props.png)

And of course, checked wireshark, and wooo, it sent another BROWSER packet!

![image](/assets/images/network-explorer-browser-packet2.png)

But this time with the `Host Comment` changed to the new one! Amazing!

A little more googling and indeed, it's used for maintaing a list of computers!

![image](/assets/images/browser-packet-description.png)
Source: [https://web.archive.org/web/20081218030331/http://www.theeldergeek.com/computer_browser.htm](https://web.archive.org/web/20081218030331/http://www.theeldergeek.com/computer_browser.htm)

I am still unsure why Windows sends those NBNS packets when Windows sends anyway the BROWSER one's. 

If you know the answer on one of my questions or if I was wrong somewhere, drop a dm on discord `d4rckh#1545`!

In the next part I will be telling you some actions that trigger those browser packets being broadcasted, stay updated!