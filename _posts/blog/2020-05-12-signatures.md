---
layout: single
title: "Digital Signatures"
excerpt: "An electronic verification of the sender."
toc: true
toc_lable: "Contents"
classes: wide
tags: [Cryptography, Kerberos, Hashing, Encryption]
---

## Equivalent to a hand written signature

Its an electronic verification of the sender.  Uses asymmetric key encryption

Purpose:
* Authentification: The message was created and sent by the claim sender.
* Non-Repudiation: Sender cant deny having  sent the message
* Integrity - Ensures the message was not altered in transit

![upload-image]({{ "/assets/imgs/notes/dg1.png" | relative_url }})

1. Bob (sender) generates two pairs of keys(public and private): Gives Alice (receiver) the public key.
2. He generates a digest / hash by hashing his messages using a hash alogorithm and encrypts it using the private key generated in the previous step.
3. He sends the digital signature together with the message to Alice.
4. Alice decrypts the digital signature using the public key. If it fails to decrypt then she knows the sender wasn't Bob.
5. Once Alice gets the digest, she hashes the message using the same hashing algorithm used by Bob and compares the two digests.