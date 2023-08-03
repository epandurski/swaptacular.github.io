---
layout: post
title: Under the Hood — Message Transport
description: >
  Explains the message transport protocol that Swaptacular servers use
  to send messages to each other.
author: Evgeni Pandurski
tags: [under-the-hood]
---

In [a previous post](/2023/04/26/under-the-hood-peer-connections/) I
explained how peers in Swaptacular’s network can establish authenticated
SSL/TLS connections between themselves.

In this post, I will continue this thread, and will explain how the servers
use the authenticated connections between the peers to send messages to each
other.

<!--more-->

But before we dive deeper into message transport protocols, let me clarify
how two random [Swaptacular network nodes](/overview/) that are not peers
(for example, an *accounting authority* node, and a *creditors agent* node),
can become peers.

## Info-bundle files

Every Swaptacular network node should publish an *info-bundle file*, which
all potential peers can obtain. The info-bundle file contains:

- The node's [root certificate](/public/docs/swpt-certificates.pdf)
  (self-signed).

- A [certificate signing
  request](https://en.wikipedia.org/wiki/Certificate_signing_request)
  (signed with root-CA's private key).

- A *directory of files*, providing additional information about the node.
  Any file can be included in this directory, but the included files most
  likely will contain various kinds of technical and contact information.
  The directory is compressed into a `.zip` file, and signed with root-CA's
  private key, so that the authenticity of the information can be verified.

Using the info-bundle files, the process of obtaining technical and contact
information about the other peer, and issuing and exchanging peer
certificates, is streamlined into 4 simple steps:

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

The main purpose of every message transport protocol is to facilitate the
reliable delivery of messages from one computer to another. This is a quite
generic problem, with a lot of already existing solutions, many of which fit
the Swaptacular's use case very well (AMQP 0-9-1, AMQP 1.0, STOMP, MQTT
etc.).

Therefore, any two Swaptacular peer nodes should be free to agree to use any
message transport protocol that suits their needs. However, because
interoperability is of huge importance, there must be at least one protocol
that all Swaptacular nodes understand. The chosen protocol is the [Simple
Text Oriented Messaging Protocol](https://stomp.github.io/) (STOMP). The big
advantages that STOMP has over most other protocols is its simplicity, and
its crystal clear specification. And contrary to what the "STOMP"
abbreviation suggests, the protocol supports binary messages as well.

In fact, in order to be fully interoperable with all other nodes, each
Swaptacular node must support only a [subset of the STOMP
protocol](/public/docs/swpt-stomp.pdf). Each node's *info-bundle file*, in
its included *directory of files*, should contain a `stomp.toml` file at the
directory root. This file should contain all the necessary technical
information about the STOMP servers that the node runs.

## Message serialization

The messages that peers send to each other are [Swaptacular Messaging
Protocol](/public/docs/protocol.pdf) (SMP) messages. In the SMP
specification, every SMP message is abstractly defined as a set of fields,
holding values of some abstract types.

However, to be sent over the network, each SMP message must be converted
into raw bytes. This is called "serialization". Again, this is a quite
generic problem, with a lot of already existing solutions (JSON, ASN.1,
Protobuf, Cap'n Proto, etc.).

In their `stomp.toml` file, Swaptacular nodes should specify the
serialization methods which they support, so that connecting peers can
choose the optimal serialization method supported by both nodes. But again,
in the name of interoperability, there must be at least one message
serialization method, which all Swaptacular nodes understand. Not
surprisingly, the blessed serialization method is the simplest one:
[JavaScript Object Notation](/public/docs/protocol-json.pdf) (JSON).

In further posts, I will try to explain in broad strokes, how the
Swaptacular Messaging Protocol works.
