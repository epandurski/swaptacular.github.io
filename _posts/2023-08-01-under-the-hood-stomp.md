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
explained how peers in Swaptacular’s network set up authenticated
connections between themselves.

In this post, I will continue this thread, and will talk about the message
transfer protocol that the servers use to send messages to each other,
through the authenticated SSL connections between the peers.

<!--more-->

In further posts, I will try to explain how the [Swaptacular Messaging
Protocol](/public/docs/protocol.pdf) works.
