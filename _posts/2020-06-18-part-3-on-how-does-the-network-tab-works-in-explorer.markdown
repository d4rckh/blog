---
layout: post
title:  "Part 3 on how does the Network tab work in explorer"
date:   2020-06-18 20:49:00 -0700
categories: windows research
---

This is the final and shortest blog post, I will showcasing my script to sniff `BROWSER` packets

![image](/assets/images/script-to-sniff-browser.png)

I will be explaining everything:

1. First we import `pyshark`.
2. I defined some colors to make the output **prettier**.
3. I made a new `pyshark.LiveCapture` instance, as I am using this on Windows I had to use `Wi-Fi`, on your Linux box it might be `ethX` or `wlanX` or even `wloX`
4. This fnuction just prints the output pretty.
5. Next we call `logBrowser` which will print: Sniffing **browser** packets
6. Next we define a function that will handle our browser packets.
7. `if` the packet is `in` **hostnames** then we ignore it.
8. `if` the packet is **not** `in` **hostnames** then we add it in **hostnames** and print a description for it with `logBrowser`
9. After defining everything we apply `print_callback` using the `apply_on_packets` function.

![image](/assets/images/script-to-sniff-browser-demo.png)