---
layout: post
title:  "Hackthebox - Blue"
date:   2020-08-05 19:24:55 -0700
categories: hackthebox_easy
tags: ctf hackthebox blue ms17_010 eternalblue metasploit
---

This will post explain the root process of Blue on hackthebox using metasploit and without

Running a simple nmap scan we can see quite a lot of ports open

```
$ nmap -A -T4 -p- 10.10.10.40
```

![image](/assets/images/htb_blue_ports.png)

We can see rpc, netbios and microsoft-ds

On port 445 we can see SMB running, nmap tells us the box runs `Windows 7 Professional SP1` and that the box is in a workgroup

Nmap also ran some scripts for us which tells us the hostname and more useful information like if message_signing is enabled (which won't help us in this case, but it's a good thing to know)

![image](/assets/images/htb_blue_scripts.png)

As the name suggest this box is probably vulnerable to ms17_010 which is Eternal Blue

# Metasploit way

We can verify that the box is vulnerable using the `use auxiliary/scanner/smb/smb_ms17_010` metasploit module and setting `RHOSTS` to `10.10.10.40`
   
After verifying this box is vulnerable to eternal blue we can start exploiting it using metasploit's `exploit/windows/smb/ms17_010_externalblue` module, do not forget to set the host to `10.10.10.40`

Looking at the options on the metasploit module we can see by default `generic/shell_reverse_tcp` which is an unstaged payload, it's also a generic shell and it won't result in a meterpreter shell. We could change it to `windows/x64/meterpreter/reverse_tcp` by changing `payload` using `set`. This __will__ result in a meterpreter shell, making things easier.

> **WARNING**: This exploit might crash the target machine.

After getting the meterpreter shell we can check using `getuid` that we are authority system.

# Manual way

**AutoBlue** is a nice way to exploit eternal blue manually, it is located @ [https://github.com/3ndG4me/autoblue-ms17-010](https://github.com/3ndG4me/autoblue-ms17-010) on github. (Just follow the instructions on the readme)

> **WARNING**: This exploit also might crash the target machine.
