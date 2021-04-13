---
layout: post
title:  "Hackthebox - Irked"
date:   2020-08-04 19:24:55 -0700
categories: hackthebox_easy
tags: ctf hackthebox irked nmap searchsploit exploit-db hexchat irc python steganography steghide ssh su shared-password metasploit exim oscp-like
---

In this post I will be showing you how to root the easy box on Hackthebox named Irked.

```bash
# Command: nmap -p- -sC -sV 10.10.10.117
Starting Nmap 7.70 ( https://nmap.org ) at 2018-11-17 14:02 EST
Nmap scan report for 10.10.10.117
Host is up (0.019s latency).
Not shown: 65528 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 6a:5d:f5:bd:cf:83:78:b6:75:31:9b:dc:79:c5:fd:ad (DSA)
|   2048 75:2e:66:bf:b9:3c:cc:f7:7e:84:8a:8b:f0:81:02:33 (RSA)
|   256 c8:a3:a2:5e:34:9a:c4:9b:90:53:f7:50:bf:ea:25:3b (ECDSA)
|_  256 8d:1b:43:c7:d0:1a:4c:05:cf:82:ed:c1:01:63:a2:0c (ED25519)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Site does not have a title (text/html).
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100024  1          33436/udp  status
|_  100024  1          50397/tcp  status
6697/tcp  open  irc     UnrealIRCd
8067/tcp  open  irc     UnrealIRCd
50397/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd
Service Info: Host: irked.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
``` 
(output slightly modified to fix formatting issues, "doesn't" has been transformed into "does not")

The ports open are:

**22**: ssh
- Useful once we get credentials
- Usually not vulnerable

**80**: http
- Runs apache
- Maybe vulnerable (in this case it wasn't, we will see later how it helped us)

**Other**: irc
- Runs UnrealIRCd

I looked at the webpage first and didn't see much.

![image](/assets/images/htb_irked_80.png)

We see a note saying: 
> IRC is almost working

So I checked out IRC next.

I openned metasploit first and searched for **UnrealIRCd** and I found on RCE exploit.

```
UnrealIRCd 3.2.8.1 - Backdoor Command Execution (Metasploit)
```

![image](/assets/images/htb_irked_ms.png)

I changed the RPORT and RHOST to 8067 and 10.10.10.117 and ran it. This resulted in a shell as the user `ircd`

![image](/assets/images/htb_irked_ircdshell.png)

I then ran `python -c 'import pty;pty.spawn("/bin/bash")'` to get a proper shell.

In the user's documents we can see a .backup file where everyone has read access to it.

![image](/assets/images/htb_irked_docs.png)

Catting it out we see the following contents

![image](/assets/images/htb_irked_backup.png)

We see the word `steg` meaning it has to do with stego. 

I then looked at the image on the webserver (because that was the only image we saw so far), downloaded it using curl and ran steghide against it using the password from `.backup` file

![image](/assets/images/htb_irked_curl.png)

![image](/assets/images/htb_irked_steghide.png)

![image](/assets/images/htb_irked_pass.png)

Using this pass using ssh on user `djmardov` we owned user.

![image](/assets/images/htb_irked_ssh.png)

Running `find / -perm /4000 2>/dev/null` we can see a suspicious SUID file: `/usr/bin/viewuser`

![image](/assets/images/htb_irked_suid.png)

When I execute the file I can clearly see it tries to run `/tmp/listusers` but fails as the file is not there.

![image](/assets/images/htb_irked_tmp.png)

Since the binary runs as root and I have access to /tmp I copied /bin/sh to /tmp/listusers and got a root shell by running /usr/bin/viewuser again.

![image](/assets/images/htb_irked_root.png)

Root has been owned :P