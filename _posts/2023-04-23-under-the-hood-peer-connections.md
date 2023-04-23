---
layout: post
title: Under the Hood — Peer Connections
description: Explains how authenticated SSL connections between Swaptacular peers work
author: Evgeni Pandurski
published: false
tags: [under-the-hood, intro]
---

In a series of posts, starting with this one, I will try to explain how
different things in Swaptacular work "under the hood", without requiring a
degree in computer science from the reader.

The topic of this post is: How the participants in Swaptacular's
peer-to-peer network, are able to set up reliable, permanent network
connections between themselves?

<!--more-->

As I [explained earlier](/overview/), Swaptacular allows different
organizations and individuals to form decentralized financial networks. For
example, if a *creditors agent* node wants its users (currency holders), to
be able to make payments in a currency that is managed by a given
*accounting authority* node, a reliable network connection should be set up,
between the creditors agent and the given accounting authority. This network
connection should be:

1. **Permanent** — Ideally, currency holders should be able to access their
   accounts "in perpetuity". In particular: replacing, or moving the servers
   of one or both of the peers to a new network location, may temporarily
   disturb the established connection, but should not destroy it.

2. **Authenticated** — Both peers should be 100% sure who they are
   communicating with.
   
3. **Encrypted** — A third party should not be able to eavesdrop on the
   communication.

Luckily, there is a very well-known technology that can easily solve all of
the above problems. This technology is
[SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security). You may have
heard that Web-sites use SSL to encrypt their traffic, but SSL is capable of
much more than that: It allows everyone to create its own [certificate
authority](https://en.wikipedia.org/wiki/Certificate_authority) (CA), which
is capable of signing [digital
certificates](https://en.wikipedia.org/wiki/Public_key_certificate), which
can form a [chain of trust](https://en.wikipedia.org/wiki/Chain_of_trust).

In Swaptacular, every node should run its own certificate authority (the
node's *root-CA*). This is not as complicated as it may sound. For example,
you may dedicate your old laptop to this job. The most difficult part is to
keep the private key of your certificate authority secret, while not losing
access to it yourself.

Fortunately, the private key for your node's root-CA will be rarely needed —
only when a new peer is added, or when new servers are installed. Therefore,
normally your private key will stay safely stored on a USB stick, encrypted
with a long password.

<div class="message">
  <img src="/images/swpt-peercerts.svg" alt="Peer Certificate Authorities">
</div>
