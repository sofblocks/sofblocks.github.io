---
layout: single
title: "Valentine - HTB"
permalink: /valentine/
tags: [HTB, CTF, Heartbleed, Dirty Cow, SSH]
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

Normal situation:
1. You read from file. Page table points to original file
2. You request write.
3. copy being created
4. page table points to copy
5. you write
6. file isn't saved because it was read-only

Race condition:
1. You read from file. Page table points to original file
2. You request write.
3. copy being created
4. Before the write is done, madvasie(DONTNEED) in the other thread caused a throwing away of the copy.
5. Memory manager only has the original mapping, thus the write goes there to the original file.
6. copy isn't saved because it was read-only. But anyway we have modified the original file.


Points to note:

* Getting the private key could have been avoided if the user did not publish his credentials on a public web server.
* Getting the passphrase could have been avoided as well if the patched version of OpenSSL was installed.
* To escalate to root privileges we had two options: (1) exploit the Dirty COW vulnerability, or (2) attach to a tmux session that was owned by root.

* Close the tmux session once you’re done instead of having it run (and accessible) the entire time. 