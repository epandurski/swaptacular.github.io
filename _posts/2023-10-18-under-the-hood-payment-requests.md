---
layout: post
title: Under the Hood — Payment Requests
description: >
  Explains how Swaptacular's payment requests work.
author: Evgeni Pandurski
tags: [under-the-hood]
published: false
---

In [a previous post](/2023/10/16/under-the-hood-digital-coins/) I explained
how Swaptacular's digital coins work. In this post, I will talk about
payment requests.

Every account in Swaptacular is [uniquely identified by an
URI](/public/docs/swpt-uri-scheme.pdf). In principal, the URI of the
recipient's account is the only thing that you need to know in order to make
a payment. In practice however, before making a payment, you may need more
information about the payment: the name of the recipient, the payment
reason, etc. Moreover, the URI of the recipient's account is a long sequence
of symbols, which is very hard to memorise or enter from the keyboard.

<!--more-->

To solve those problems, ...
