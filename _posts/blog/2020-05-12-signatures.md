---
layout: single
title: "Digital Signatures"
excerpt: "An electronic verification of the sender."
toc: true
toc_lable: "Contents"
classes: wide
tags: [Cryptography, Encoding, Hashing, Encryption, SSL]
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
The signature is valid when the hash values are equal.

However digital signature doesn't truly verifies the identity of the sender, as hackers may get their hands on the public key and intefere with signature together with the data itself.

## Hashing

Creates a unique digital fingerprint data called digest / hash / message digest.

![upload-image]({{ "/assets/imgs/notes/hash.png" | relative_url }})

It is primarily used for comparison purpose not for encryption.
 1. You can't reverse a hash to determine the original data. It's not reversible, unlike in encoding (a different representation of an input).
 2. Fixed size.

It's for these features that it is used for storing passwords

## Digital Certificates

As mentioned in the digital signature section, digital signature does not verify true identity of the sender hence digital certificate intervenes. 
These are electronic credentials issued by a trusted third party. It verifies the identity of the owner and shows that the owner owns the public key.

![upload-image]({{ "/assets/imgs/notes/oc.gif" | relative_url }})

Bob attaches a digital certificate to the signed message:
It contains
1. Owners name
2. Owner public key and its expiration date.
3. Issuer's name
4. Issuer's digital signature


## SSL Certificate

SSL stands for Secure Sockets Layer, a global standard security technology that enables encrypted communication between a web browser and a web server.
SSL ensures a private “conversation” just between the two intended parties. So it's basically a digital certificate of web server. It verifies identity of the web server and its public key.

![upload-image]({{ "/assets/imgs/notes/sslf.jpg" | relative_url }})

1. Browser request secure pages from the web server.
2. Web server replies back with its ssl certificate (which contains its publick key) digitalLy signed by a third party or CA ( certificate authotity).
3. The browser checks for the issuer's (CA) digital certificate to validate it. It does this by running through its database containing various public keys (previously installed) of various Certificate Authorities. 
4. If the cerificate is valid -- > its indeed signed by the said certificate authority, the browser lights up a green padlock at the address bar simply indicating that certificates really belongs to the web server.
5. The browser generates a pair of symmetric secret keys, encrypts one of the keys using the web server's public key and sends it over.
6. The web server uses its private key to decrypt the message and obtain the secret key. Both parties uses the key to encrypt and decrypt any communication between them.

In this scenario we see how asymmetric and symmetric key algorithm works together.
- Asymmetric key is used to verify the identity of the owner and the public key so that trust is built.
- Once a connection has been established, symmetric key algorithm is used to encrypt and decrypt the connection between the two hosts.

`SSL and the green padlock indicates that the connection is encrypted but does not mean that the website itself is secure.`

![upload-image]({{ "/assets/imgs/notes/cert.png" | relative_url }}) 
![upload-image]({{ "/assets/imgs/notes/cert2.png" | relative_url }})

## SSL / TLS Handshake Protocol

Handshake protocal is involved with the top 3 layers in osi model and 1st in tcp/ip  model
1. client sends a message to the server containing : ssl / tls version, cryptogrtaphic algorithms and data compressing methods suported by the client.
2. server responds with a message containing: cryptogaphic algorithm chosen from the list provided by the client, session id, its digital certificate and its pubic key
3. client will contact server CA and verify the server digital certificate so as to established trust
4. client sends a shared key encrypted with the server's public key to the server.
5. client sends a finished message encrypted with the shared secret key indicating the client part of hanshake is complete.
6. server side responds with a final message encrypted with secret key indicating the handshake protocal is complete.
once the handshake protocal is done, the client and the server can now exchange encrypted messages.