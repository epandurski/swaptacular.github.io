---
layout: post
title: Under the Hood — Peer Connections
description: Explains how authenticated SSL connections between Swaptacular peers work
author: Evgeni Pandurski
published: false
tags: [under-the-hood, intro]
---

In a series of posts, starting with this one, I will try to explain how
different things in Swaptacular work "under the hood", hopefully without
requiring a degree in computer science from the reader.

The topic of this post is: How peers in Swaptacular's network set up
reliable connections between themselves?

<!--more-->

[As I explained earlier](/overview/), in Swaptacular, different
organizations and individuals can connect with each other, and form a
decentralized financial network. For example, if a *creditors agent* wants
its users (that is: currency holders), to be able to make payments in a
currency that is managed by a known *accounting authority*, a reliable
network connection should be set up, between the creditors agent and the
accounting authority. This network connection should be:

1. **Permanent** — Ideally, currency holders should be able to access their
   accounts "in perpetuity". In particular: replacing, or moving the servers
   of one or both of the peers to a new network location, may temporarily
   disturb the established connection, but should not destroy it.

2. **Bi-directional** — Both peers should be able to initiate a client
   connection to other peer's servers, and send messages. This way, if there
   are no messages to be sent, the network connection can be temporarily
   closed, and re-opened again when needed.

3. **Authenticated** — Both peers should be able to verify with 100%
   certainty who they are communicating with.
   
4. **Encrypted** — A third party should not be able to eavesdrop on the
   communication.

Luckily, there is a very well-known technology that meets the above
requirements. This technology is
[SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security) ("Transport
Layer Security" is another name for it). You may have heard that Web-sites
use SSL to encrypt their traffic, but SSL is capable of much more than that:
It allows everyone to create its own [certificate
authority](https://en.wikipedia.org/wiki/Certificate_authority) (CA), which
can sign [digital
certificates](https://en.wikipedia.org/wiki/Public_key_certificate), and
those certificates can form a [chain of
trust](https://shagihan.medium.com/what-is-certificate-chain-and-how-to-verify-them-be429a030887).

In Swaptacular, every network node runs its own trusted certificate
authority (*root-CA*), and issues a self-signed certificate to itself. This
is not as complicated as it may sound. [Using these simple
scripts](https://github.com/swaptacular/swpt_ca_scripts), you can easily
turn your old laptop into a Swaptacular certificate authority. The most
difficult part will be to keep your root-CA private key secret, while not
losing access to it yourself. Fortunately, you will not use this private key
very often, and normally it will stay safely stored on an USB stick
somewhere, encrypted with a long password.

Instead of using your precious root-CA private key directly, you will use it
only to sign *peer certificates*, and *server certificates*.

- **Peer certificates** you give to your peers, so that they can prove their
  identity before your servers.

- **Server certificates** will be used by your servers, so that they can
  prove their identity before your peers. Losing the private key for a
  server certificate is not a problem, as long as it has not been stolen by
  hackers — you simply generate a new private key for your servers, and use
  your root-CA private key to sign a new server certificate.

A picture is worth a thousand words:

<div class="message">
  <img src="/images/swpt-peercerts.svg" alt="Peer Certificate Authorities">
</div>

For example, if Peer 1's servers want to open a client connection to Peer
2's servers, they will be able to authenticate themselves by presenting the
following chain of certificates:

* Peer 1's Server Key
* Peer 1's Root CA
* Peer 2's Root CA — **trusted by Peer 2's servers!**

On the other hand, Peer 2's servers in order to prove to the connecting
clients, that they really are the Peer 2's servers, will present the
following chain of certificates:

* Peer 2's Server Key
* Peer 2's Root CA — **exactly what Peer 1's servers expected!**

A small, but very important technical detail here is that all peer
certificates allow the other peer's root-CA, to act as an intermediate
certificate authority (sub-CA), but do not allow it to alter the *subject
name* on the subsequently signed certificates. This gorgeous SSL feature is
called "[name
constraints](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.10)".

-----

In further posts, I will talk about the message transfer protocol that the
servers use to send messages to each other, through the authenticated SSL
connections between the peers.
