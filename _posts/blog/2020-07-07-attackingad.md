---
layout: single
title: "Attacking AD"
permalink: /ad-attacks/
tags: [Active Directory, mim, LLMNR]
toc: true
---

## What is Active Directory

It's a directory service developed to manage domain networks. It stores information related to objects such as computers, printers, users etc.

## Authentication Protocols

Two authentication protocols used are NTLM and Kerberos.

### NTLM

New Technology LAN Manager (NTLM) workes on the basis of challenge and response.

The user provides their username, password, and domain name at the interactive logon screen of a client.

The client develops a hash of the user’s password and discards the actual password.

The client sends the username in plain text to the server it wants to access.

The server sends a challenge to the client. This challenge is a 16-byte random number or a variable length  (NTLMv2).

The client then sends a response to the server. This response is the challenge encrypted by the hash of the user’s password.

The server sends the challenge, response, and username to the domain controller (DC).

The DC retrieves the hash of the user’s password from its database, and then encrypts the challenge using it.

The DC compares the encrypted challenge it has computed (in the above step) to the response of the client. If these two match, the user is authenticated.

### Kerberos

Our blog post on [Cryptography](/cryptography) explains more in depth how kerberos works.

The user attempts to join the network through the client’s interactive logon screen.

The client constructs a package called an authenticator which has information about the client (username, date, and time). Except for the username, all the other information contained in the authenticator is encrypted with the user’s password.

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


## Windows Name Resolutions:

[DNS, NETBIOS, LLMNR]

Most windows operating system adopts this sequency:
Host file -> Dns Server -> LLMNR -> NBT-NS

Host file -- >  a file located in an operationg system which maps hostnames to ip addresses.

DNS ---> A distributed system of name resolvers.

LLMR ---> Link-Local Multicast Name Resolution

