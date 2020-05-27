---
layout: single
title: "Valentine - HTB"
permalink: /valentine/
tags: [HTB, CTF, Heartbeat, Dirty Cow, SSH]
toc: true
---

Takeaway points:

## sslyze tool

Python tool that can analyze the SSL configuration of a server.(bad certificate, weak cipher suites, Heartbleed, ROBOT, TLS 1.3 support, etc.).

```python
sslyze --heartbleed 10.10.10.79
```

## Xclip tool

 Allows you to put the output of a command directly into the clipboard so that you don't have to copy&paste from the terminal manually.

 ```python
    xclip file_name  --- use the middle button on the mouse to paste text
    xclip -sel clip file_name

    tail -n 30 logfile.log | xclip -sel clip
 ```
## The Heartbleed Bug  (CVE-2014-0160)

Allows anyone on the Internet to read the memory of the systems protected by the **vulnerable versions of the OpenSSL** software
This compromises the secret keys used to identify the service providers and to encrypt the traffic, the names and passwords of the users and the actual content. This allows attackers to eavesdrop on communications, steal data directly from the services and users and to impersonate services and users. 

Get graphical representation [here](https://xkcd.com/1354/)

## Dirty COW (CVE-2016-5195)

Is a computer security vulnerability (priviledge escalation exploit) for the Linux kernel that affects all Linux-based operating systems including Android that use older versions of the Linux kernel. Existed in the Linux Kernel since 2007, but was only discovered and exploited in 2016.
Malicious programs can potentially set up a race condition to turn a read-only mapping of a file into a writable mapping. Thus, an underprivileged user could utilize this flaw to elevate their privileges on the system.