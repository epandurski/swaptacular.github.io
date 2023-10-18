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
*payment requests*.

Every account in Swaptacular is [uniquely identified by an
URI](/public/docs/swpt-uri-scheme.pdf). In principal, the URI of the
recipient's account is the only thing that you need to know in order to make
a payment. In practice however, before making a payment, you may need more
information about the payment: the name of the recipient, the payment
reason, the payment deadline etc.

<!--more-->

Moreover, the URI of the recipient's account is a long sequence of
random-looking symbols, which is very hard to memorize or enter from the
keyboard. Therefore, there must be a standard way for the payer and the
payee to exchange this information conveniently.

To solve these and other related problems, an extendable set of
specifications must be in place, so that conforming client applications can
work together seamlessly, and yet, not be limited by the "lowest common
denominator".

## Payment requests in Swaptacular

- [Payment Requests and Transfers in
  Swaptacular](/public/docs/payment-requests.pdf)
- Generic payment requests
- Payee reference, transfer note, and transfer note formats

## The "PR-zero" file format

- [PR-zero Payment Request Documents](/public/docs/pr0-documents.pdf)
- Other may be standardized in the future
- Payment request files and QR codes

## Conclusions

TODO
