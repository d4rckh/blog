---
layout: post
title:  "Hackthebox - Writeup"
date:   2020-08-03 19:24:55 -0700
categories: hackthebox writeups easy
tags: ctf hackthebox nmap cmsms sqli credentials injection 
---

Today we will be solving **Writeup** from hackthebox which was an **easy** box

I ran an nmap scan against the box on IP 10.10.10.138 using the following command

```bash
nmap -T4 -A -oA nmap/writeup 10.10.10.138
```

![image](/assets/images/htb_writeup_ports.png)

Looking at the results we can see the following services open:

**Port 22**: SSH
- Once we get some creds we could try logging in with them using SSH
- Not the best target

**Port 80**: HTTP 

Going to the webpage we see the following:

![image](/assets/images/htb_writeup_webpage.png)

Looking at the above note we see that they use some type of DDoS protection (we will find out later that it uses fail2ban)

If we look back at the NMAP results we can see the `http-robots.txt` script being ran revealing a /writeup/ page, going to it we can see a small list of writeups

![image](/assets/images/htb_writeup_webpage_writeups.png)

By looking at the source-code we can identify the CMS which was being used, in this case it is **CMS Made Simple**

![image](/assets/images/htb_writeup_cms.png)

Searching for results on google we can see one that pops out, which is an SQL Injection vulnerability 

![image](/assets/images/htb_writeup_vuln.png)

Downloading and running the exploit gets us an error saying that termcolor is not installed.

I am using parrot right now and `pip2` was not installed by default so you should be able to install it using 

```bash
$ sudo apt install python2-pip
```

and then install `termcolor` using

```bash
$ pip2 install termcolor
```

The exploit should run fine afterwards

![image](/assets/images/htb_writeup_exploit.png)

Running it using the following:

```bash
$ python exploit.py -u http://10.10.10.138/writeup/ --crack --wordlist path/to/rockyou.txt
```

After 30 minutes it finally got cracked

![image](/assets/images/htb_writeup_exploit_res.png)

We could use hashcat and crack it faster.

We then use the credential to login using ssh

![image](/assets/images/htb_writeup_ssh.png)

And we owned user.

Next up I decided to run linpeas, it is located at carlospolop/privilege-escalation-awesome-scripts-suite on github

I ran a http server using python to serve linpeas.sh

![image](/assets/images/htb_writeup_serve.png)

Then downloaded it using wget on the box

![image](/assets/images/htb_writeup_wget.png)

After running it I couldn't have anything useful, so I ran pspy and started monitoring new processes, one of them popped out interesting which is triggering when a new user logins.

![image](/assets/images/htb_writeup_pspy.png)

Looking at the /etc/update-motd.d/10-uname we can see it runs `uname`

Running whereis `uname` we find out it is located in `/bin`

Using `path` we can find out that `/usr/local/bin` is prioritized meaning it will try searching for uname there first.

As we got access to write in `/usr/local/bin` we can just write our own fake uname which would put `/root/root.txt` in our home directory:

```bash
$ echo -e '#!/bin/bash\ncp /root/root.txt /home/jkr/secret.txt' > /usr/local/bin/uname
$ chmod +x /usr/local/bin/uname
```

Afterwards we relogin using SSh and we get the root flag in jkr's home directory

Alternatively we could put a reverse shell there and get a root shell.