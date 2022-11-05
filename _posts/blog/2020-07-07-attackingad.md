---
layout: single
title: "Attacking AD"
permalink: /ad-attacks/
tags: [Active Directory, mim, LLMNR, kerberos. IPv6]
toc: true
header: 
    teaser: "assets/imgs/notes/attackad.png"
---

## What is Active Directory

It's a directory service developed to manage domain networks. It stores information related to objects such as computers, printers, users etc.

## Authentication Protocols

These are protocols that AD use to authenticate users, computers, and services in AD, and enable authorized users and services to access resources securely.

Two main protocols used are NTLM and Kerberos.

### NTLM

New Technology LAN Manager (NTLM) works on the basis of challenge and response.

The user provides their username, password, and domain name at the interactive logon screen of a client.

The client develops a hash of the user’s password and discards the actual password.

The client sends the username in plain text to the server it wants to access.

The server sends a challenge to the client. This challenge is a 16-byte random number or a variable length  (NTLMv2).

The client then sends a response to the server. This response is the challenge encrypted by the hash of the user’s password.

The server sends the challenge, response, and username to the domain controller (DC).

The DC retrieves the hash of the user’s password from its database, and then encrypts the challenge using it.

The DC compares the encrypted challenge it has computed (in the above step) to the response of the client. If these two match, the user is authenticated.

### Kerberos

Our blog post on [Cryptography](/cryptography) explains in depth how kerberos works.

The user attempts to join the network through the client’s interactive logon screen.

The client constructs a package called an authenticator which has information about the user (username, date, and time). Except for the username, all the other information contained in the authenticator is encrypted with the user’s password.

The client then sends the encrypted authenticator to the KDC (Key Distribution Center).

The KDC immediately knows the identity of the client that has sent the authenticator by looking at the username. The KDC will then look into its AD database for the user’s password, which is a shared secret. It then decrypts the authenticator with the password. If the KDC is able to decrypt the authenticator, it means that the identity of the client is verified.

Once the identity of the client is verified, the KDC creates a ticket granting ticket (TGT), which is encrypted by a key that only the KDC knows.
The KDC sends the TGT to the client. The client stores the TGT in its Kerberos tray. It can use this ticket whenever it needs to access a resource on a server on the network (within a typical time limit of eight hours).

When the client needs to access another server, it sends the TGT to the KDC along with a *request* to access the resource.

The KDC decrypts the TGT with its key. This step verifies that the client has previously authenticated itself to the KDC.
The KDC generates a ticket for the client to access the shared resource. This ticket is encrypted by the *server’s key*. The KDC then sends this ticket to the client.

The client saves this ticket in its Kerberos tray, and sends a copy of it to the server.
The server uses its own password to decrypt the ticket

If the server successfully decrypts the ticket, it knows that the ticket is legitimate. The server will then open the ticket and decide whether the client has the necessary permission to access the resource by looking through the access control list (ACL).

### LDAP

Only windows devices can authenticate to AD using the above discussed protocols, so what happens to non-windows devices such as firewall or Linux machines, how do they authenticate to AD.

Well we have other protocols such as RADIUS and LDAP (Lightweight Directory Access Protocol)

LDAP is an open and cross platform protocol used for directory services authentication. It enable communication between different directory services servers.

## Windows Name Resolutions:

[DNS, NETBIOS, LLMNR]

Most windows operating system adopts this sequency of hostname to IP resolution :     
Host file -> Dns Server -> LLMNR -> NBT-NS

Host file -- >  a file located in an operationg system which maps hostnames to ip addresses.

DNS ---> A distributed system of name resolvers.

LLMR ---> Link-Local Multicast Name Resolution, allows both IPv4 and IPv6 hosts to perform name resolution for hosts on the same local link. Sends a network packet to UDP port 5355 to the multicast network address.

NBT-NS --->  Resolves a NetBios name (16-byte string) to one or more IP addresses. An example of a process that uses a NetBIOS name is the File and Printer Sharing for Microsoft Networks service.


## Exploiting Windows Name Resolution

### LLMR Poisoning:

In a case where windows machine cannot resolve a hostname using DNS or the local host file, it resorts to LLMNR or NBT-NS protocols and ask the neighbouring computers on the network about it.

This may be caused by:

* A user accidently typing a wrong network share.
* A broken file share or a network printer

Now remember LLMNR works by sending network packets to UDP port 5355 to the multicast network address or TCP port 139/445 in the case of NBT-NS, asking all nodes on the network to reply if they are authoratively known as the hostname in query.

A hacker somewhere on the network may intercept the traffic on UDP port 5355 or Tcp port 137/139/445 and respond by claiming that they are authoratively known as the hostname in query.  If the requested host belongs to a resource that requires identification/authentication, the username and NTLMv1/ NTLMv2 hash will then be sent to the hacker's system. The attacker can then grab the hash and try to crack it using tools such as hashcat or john the ripper.

Using python responder tool to demostrate the LLMNR expoit.      
An attacker spins up the tool on their system, and start listening on the main network interface.

```yml
  > responder -I eth0 -rdwv 
```
A victim on the other hand accidently types a wrong network share

![upload-image]({{ "/assets/imgs/notes/rshare.png" | relative_url }})

The python responders spoofs the request and the victim host responds with domain username and NTLMv2 hash.

![upload-image]({{ "/assets/imgs/notes/responder.png" | relative_url }})

### LLMNR Poisoning Mitigation

* Disable LLMNR:

Local group policy -- > Computer configuration --> Administrative templates --> Network --> Dns client --> Turn off multicast name resolution : Enable

![upload-image]({{ "/assets/imgs/notes/llmnr.png" | relative_url }})

* Disabling NBT-NS (NetBIOS Name Service):

![upload-image]({{ "/assets/imgs/notes/nbt.png" | relative_url }})



## Windows IPv6 Attacks

Many company networks are yet to adopt IPv6 system, as IPv4 protocol is still prevalent. However almost every windows OS version have IPv6 feature enabled by default even though the protocol being used is IPv4.

Windows queries for IPv6 configuration regularly as shown in the figure below of wireshark capture packet.

![upload-image]({{ "/assets/imgs/notes/wrsh.png" | relative_url }})

If IPv6 is set up for auto-configuration but no configuration servers are on the network, an attacker sitting on the same network can set up a rogue server to answer configuration requests. 
Modern operating systems prefer IPv6 over legacy IPv4 and will use a rogue IPv6 connection by default if one is available.  This allows an attacker to hijack traffic such as Domain Name System lookups, creating a potentially bad security problem such as Active Directory Attack.

To demostrate this attack we will use python tool called mim6 by fox-IT company. You can ran it across all targets on a network or pin point a specific target.

```yml
> mitm6 -d sofblocks.com  ----> spins up a IPv6 dhcp server and starts responding to different requests on the network:
```
Will Wait for a client/victim to request for ipv6 via dhcpv6 or request for wpad configuration. Best chance is when the victim reboots or unplug network cable.
mitm6 will reply to those DHCPv6 requests, assigning the victim an IPv6 address within the link-local range. 

![upload-image]({{ "/assets/imgs/notes/mitm.png" | relative_url }})

Quickly on the other end the victim's machine adopts the attacker's IPv6 rogue DNS server:

![upload-image]({{ "/assets/imgs/notes/victim.png" | relative_url }})

As soon as this happens the victim will start querying for the WPAD configuration of the network. Since these DNS queries are sent to the attacker, it can just reply with  with a fake wpad file with its own IP address set up as a proxy.

### WPAD

Windows Proxy AutoDiscovery protocol automatically detect a network proxy used for connecting to the internet in corporate environments.
When the victim launches a browser to access any site, it will use the attacker's machine as the proxy. Attacker replies with a HTTP 407 Proxy Authentication required normally HTTP uses HTTP 401.     
The victim will send NTLM hash to the attacker, who can relay it to different services. With this relaying attack, the attacker can authenticate as the victim on services, access information on websites and shares, and if the victim has enough privileges, the attacker can even execute code on computers or even take over the entire Windows Domain.

Mim6 tool will be used to spoof the DNS so we will need another tool to serve WPAD file to the victim and prompt them for authentication.
For this we will use a tool called ntlmrelayx which is part of the impacket Python library. The tool will also be used to relay the victims credentials to other servers on the network.

```yml
> ntlmrelayx.py -6 -t ldaps://192.168.159.144 -wh fakewpad.sofblocks.com -l juicy

> ntlmrelayx.py -6 -t smb://192.168.159.144 -wh fakewpad.sofblocks.com -t -l readme 
```
### Attack

A user with admin privileges logs into one of the workstations within the spoofed network. In a couple of minutes, ntlmnrelayx tool captures the admin hash and relays them to the domain controller and begins to attack.

![upload-image]({{ "/assets/imgs/notes/ldaps.png" | relative_url }})

It further attempts to enumerate active directory and creates a new user.

![upload-image]({{ "/assets/imgs/notes/attack.png" | relative_url }})

We can confirm that the user has been added in active directory.

![upload-image]({{ "/assets/imgs/notes/user.png" | relative_url }})

### Mitigations

The best way to guard against these attacks is to disable IPv6 if its not in use in your network, however this is not recommended.
Many newer applications may fail to work correctly if ipv6 is disabled, plus its just a matter of time before it becomes mandatory as the address pool continues to shrink.   
So the best thing to do is to have IPv4 favoured over IPv6.

First check the IPv6 precedence:
```yml
> netsh interface ipv6 show prefixpolicies
```
![upload-image]({{ "/assets/imgs/notes/ipv6.png" | relative_url }})

IPv6 addresses are preferred as ::/0 and has a precedence of 40.

Next command will make IPv4 addresses have a precedence of 46.

```yml
> netsh interface ipv6 set prefixpolicy ::ffff:0:0/96 46 4
```

There are many blog posts out there that describes how to disable WPAD and how to mitigate against NTLM relay attacks.

References:

[mitm6 – compromising IPv4 networks via IPv6](https://blog.fox-it.com/2018/01/11/mitm6-compromising-ipv4-networks-via-ipv6/)



