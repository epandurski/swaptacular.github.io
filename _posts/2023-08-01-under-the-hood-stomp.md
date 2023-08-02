---
layout: post
title: Under the Hood — STOMP
description: >
  Explains the message transport protocol that Swaptacular servers use
  to send messages to each other.
author: Evgeni Pandurski
tags: [under-the-hood]
published: false
---

In [a previous post](/2023/04/26/under-the-hood-peer-connections/) I
explained how peers in Swaptacular’s network establish authenticated SSL/TLS
connections between themselves.

In this post, I will continue this thread, and will explain how the servers
use the authenticated connections between the peers to send messages to each
other.

<!--more-->

Before we dive deeper into message transport protocols, let me explain how
two random Swaptacular network nodes (an *accounting authority* node and a
*creditors agent* node, for example) can become peers.

## Info-bundle files

Every Swaptacular network node should publish an *info-bundle file*, which
all potential peers can obtain. The info-bundle file should contain:

- The node's *root certificate* (self-signed).

- A *certificate signing request* (signed with root-CA's private key).

- A *directory of files*, providing additional information about the node.
  Any file can be included in this directory, but the included files most
  likely will contain various kinds of technical and contact information.
  The directory is "zipped" and signed with root-CA's private key, so that
  the authenticity of the information can be verified.

With the info-bundle files, the process of issuing and exchanging peer
certificates is streamlined into 4 simple steps:

<div class="message">
  <img src="/images/peers-infobundles.svg"
       alt="The 4 steps of issuing and exchanging peer certificates">
</div>

**Note:** The above 4 steps can be performed in a slightly different order,
and the final result will be the same.

## The STOMP protocol

Now that we know how Swaptacular nodes issue peer certificates to each
other, and how they can use them to establish authenticated SSL/TLS
connections, we can talk in more detail about message transport protocols.

TODO

In further posts, I will try to explain how the [Swaptacular Messaging
Protocol](/public/docs/protocol.pdf) works.
