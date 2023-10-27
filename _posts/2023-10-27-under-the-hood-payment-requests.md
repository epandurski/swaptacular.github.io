---
layout: post
title: Under the Hood — Payment Requests
description: >
  Explains how Swaptacular's payment requests work.
author: Evgeni Pandurski
tags: [under-the-hood]
---

In [a previous post](/2023/10/16/under-the-hood-digital-coins/) I explained
how Swaptacular's digital coins work. In this post, I will talk about
*payment requests*.

> In the general sense, a request for payment (or a payment request) refers
> to any communication sent out to customers asking for them to pay for
> goods and services.

<!--more-->

Every account in Swaptacular is [uniquely identified by an
URI](/public/docs/swpt-uri-scheme.pdf). In principal, the URI of the
recipient's account is the only thing that you need to know in order to make
a payment. In practice however, before making a payment, you may need more
information about the payment: the requested amount, the name of the
recipient, the payment reason, the payment deadline etc.

Moreover, the URI of the recipient's account is a long sequence of
random-looking symbols, which is very hard to memorize or enter from the
keyboard. Therefore, there must be a standard way for the payer and the
payee to exchange this information conveniently.

In order to solve these and other related problems, Swaptacular gives a set
of recommendations, so that conforming client applications can work together
seamlessly.

## Payment requests in Swaptacular

The ["Payment Requests and Transfers in
Swaptacular"](/public/docs/payment-requests.pdf) specification defines what
exactly payment requests in Swaptacular are, and how they work together with
transfers. In summary, there are several important things that should be
noted:

1. Swaptacular's payment requests can be in different **file formats**. If
   need be, new standard formats can be added to the existing ones.

2. The payment request usually contains a **payee reference**. A payee
   reference is a relatively short sequence of characters (an invoice number
   for example), that the payer should include alongside the payment to help
   the payee identify it.

3. Every Swaptacular transfer (and thus every payment) has a **transfer
   note**. The transfer note may contain any information that the sender
   wants the recipient of the transfer to see.

4. The *payee reference* should be included in the *transfer note*.

5. Transfer notes can be in different **transfer note formats**. If need be,
   new standard formats can be added to the existing ones. For *canonical
   formats* however, the first line of the transfer note should always
   contain the payee reference.

6. Normally, payment requests do specify the requested amount. Payment
   requests which do not specify a requested amount, or the specified amount
   is zero, are called **generic payment requests**.

   Generic payment requests can be used to solicit recurring transfers. A
   generic payment requests basically says: "Here is my account, you can
   send me money anytime you want".

## The "PR-zero" payment request file format

Swaptacular's payment requests are "normal" computer files, and as such,
they can be sent to the payer via countless number of channels. It is very
important, however, to also be able to present payment requests as [QR
codes](https://en.wikipedia.org/wiki/QR_code).

The ["PR-zero Payment Request Documents"](/public/docs/pr0-documents.pdf)
specification defines a compact payment request file format which is well
suited for QR codes. All conforming client applications should be able to
process payment requests in the "PR-zero" format.

## Conclusions

To allow different client applications to process payment requests
interoperably, and yet, not be limited to the lowest common denominator,
Swaptacular gives a **minimal but extendable** set of recommendations.
