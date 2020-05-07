---
title: "About"
permalink: "/about/"
author_profile: true
---

> Sysadmin|Penetration Tester|Infosec perpetual learner.

A rose smells better than a cabbage, but that doesn't mean it can make a better stew. Don't try to compare yourself too much  to others. `You also have your own strength search it and build on it`. You may make better stew than they think.


If God could wait long enough for snails to enter Noah's ark, His door of grace won't close till you reach your expected position in life. Never look down on yourself keep looking up.



title: Cryptography
author: Sofblocks
date: 2020-04-29 18:55:00 +0300
categories: [Infosec, Hacking]
tags: [crypto]


## What is Kerberos?

> `Cryptography` is the science of secret writing with the intention of keeping the data secret. 
Cryptography is classified into symmetric cryptography, asymmetric cryptography and hashing. 

[**Kerberos**] in Greek mythology was a three-headed hound of Haides.

Kerberos is a protocol for authenticating service requests between trusted hosts across an untrusted network, such as the internet.
Kerberos is built in to all major operating systems, including Microsoft Windows, Apple OS X, FreeBSD and Linux.

Since Windows 2000, Microsoft has incorporated the Kerberos protocol as the default authentication method in Windows, and it is an integral component of the Windows Active Directory service. Broadband service providers also use Kerberos to authenticate DOCSIS cable modems and set-top boxes accessing their networks.

It involves 3 parties: client, server and key distribution center(KDC)

 KDC is made up of:
 > AS  -- > Authentication server |
   TGS -- > Ticket Granting server.

![upload-image]({{ "/assets/images/sample/kdc.png" | relative_url }})

1. Client sends a request to the AS along with his/her file server credentials. The client uses the password as a secret key to encrypt the message.
2. AS looks for the client's credentials in its database and uses client's password to decrypt the requeast.
3. After authenticating the client, AS send a TGT (ticket granting ticket) to the client which is encrypted with a another secret key.
4. Client forwards the TGT to the TGS along with their request like "i want to access file server".
5. Upon receiving the request TGS decrypts the request with the secret key shared by AS.
6. TGS issues the client with a tocken encrypted with another secret key which is shared with the File server.
7. The client sends the service tocken to the file server.
8. The file server decrypts the service token with a key shared by TGS if the decryption is successful, it allows the client to use its resources for acertain period of time as indicated in the token.

![upload-image]({{ "/assets/img/sample/kdc1.png" | relative_url }})

A good analogy of a service token is a movie ticket.

Kerberos authentication uses Symmetric Key Encrypion cryptography.
 
## **Private key Cryptography** | **Symmetric Encryption**

In Private key encryption:
Data is encrypted using a single key known only to the sender and receiver.
Private  key encryption is also called symmetric encryption / cryptography, because a single key is used in both encryption and decryption.

![upload-image]({{ "/assets/img/sample/pk.png" | relative_url }})

Private key encryption methods:
1. Stream ciphers -- > works on a single bit at a time.
2. Block ciphers  -- > operates on a fixed block of bits, data comes in chunks
   Examples . AES

The biggest problem whith this cryptography method is that one needs a way to share the private key with the party to whom to share the data.
If someone was to get his hands on the key that person will be able to decrypt any info sent between the two parties.


## **Public key Cryptography** 

In Public key, two keys are used one key is used for encryption and another key is used for decryption. One key (public key) is used for encrypt the plain text to convert it into cipher text and another key (private key) is used by receiver to decrypt the cipher text to read the message.

   Public key encryption to encrypt the sender message starts with the receiver/
1. The receiver creates a pair of keys, a private and a public. The receiver keeps the private key and sends the public key to the sender.
2. The sender encrypts his message with the public key
3. When the receiver gets the message she decrypts it using the private key.

 * Public key encryption to encrypt the sender message starts with the receiver/
1. The receiver creates a pair of keys, a private and a public. The receiver keeps the private key and sends the public key to the sender.
2. The sender encrypts his message with the public key
3. When the receiver gets the message she decrypts it using the private key.