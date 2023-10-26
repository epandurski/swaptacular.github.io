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

To solve these and other related problems, a set of specifications is in
place, so that conforming client applications can work together seamlessly.

## Payment requests in Swaptacular

> In the general sense, a request for payment refers to any communication
> sent out to customers asking for them to pay for goods and services.

The ["Payment Requests and Transfers in
Swaptacular"](/public/docs/payment-requests.pdf) specification defines what
exactly payment requests in Swaptacular are, and how they work together with
transfers. In summary, there are several important things to note:

1. Payment requests can use different file formats. If need be, new standard
   formats can be added to the existing ones.

2. The payment request may contain a **payee reference**. A payee reference
   is a relatively short sequence of characters (an invoice number for
   example), that the payer should include alongside the payment to help the
   payee identify it.

3. Every Swaptacular transfer (and thus every payment) has a **transfer
   note**. The transfer note may contain any information that the sender
   wants the recipient of the transfer to see.

4. The *payee reference* should be included in the *transfer note*.

5. Transfer notes can be in different **transfer note formats**. If need be,
   new standard formats can be added to the existing ones. For *canonical
   formats* however, the first line of the transfer note always contains the
   payee reference.

6. Normally, payment requests do specify the requested amount. Payment
   requests which do not specify a requested amount, or the specified
   requested amount is zero, are called **generic payment requests**.

7. Generic payment requests can be used to solicit recurring transfers. A
   generic payment requests basically says: "Here is my account, you can
   send me money anytime you want".

## The "PR-zero" file format

We saw in the previous section, that Swaptacular's payment requests are just
"normal" files, and as such, they can be sent to the payer via countless
number of channels. However, it is also very important to be able to send
payment requests as [QR codes](https://en.wikipedia.org/wiki/QR_code).

The ["PR-zero Payment Request Documents"](/public/docs/pr0-documents.pdf)
specification defines a compact file format which is well suited for QR
codes.

## Conclusions

In order to allow different client applications to process *payment
requests* seamlessly, and yet, not be limited to the "lowest common
denominator", Swaptacular gives a minimal but extendable set of
specifications.
