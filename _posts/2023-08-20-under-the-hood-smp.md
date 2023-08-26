---
layout: post
title: Under the Hood — SMP
description: >
  Explains the basics of the Swaptacular Messaging Protocol.
author: Evgeni Pandurski
tags: [under-the-hood]
published: false
---

In [a previous post](/2023/08/03/under-the-hood-message-transport/) I
explained how peers in Swaptacular’s network use the STOMP message transport
protocol, to send Swaptacular Messaging Protocol messages to each other.

In this post will try to explain how the Swaptacular Messaging Protocol
(SMP) works.

<!--more-->

In a nutshell, SMP specifies how currency holders can open and close
accounts in different currencies, and how they can make and receive payments
from/to these accounts. So, the basic idea is quite simple, but there are a
lot of nitty-gritty details which complicate the matter. Here, I will
concentrate on the big picture, but you can always check [the protocol
specification](/public/docs/protocol.pdf) for the details.

## Debtor and creditor IDs

In SMP, every account is identified by a (`debtor_id`, `creditor_id`) number
pair. Both debtor IDs, and creditor IDs are 64-bit integer numbers (that is:
numbers with up to 20 digits). Every Swaptacular currency issuer (aka
debtor) receives a globally unique number — its debtor ID. This works by
arranging [accounting authority nodes](/overview/) to be responsible for
pre-allocated, non-overlapping intervals of debtor IDs.

While debtor IDs are globally unique, creditor IDs are not. Every accounting
authority node is free to decide which interval of creditor IDs to allocate
to each one of its peer *creditors agents*. Therefore, creditors agents have
no way of knowing in advance, what range of creditor IDs each one of their
peer nodes will allocate for them. To solve this problem, creditors agents
may use "virtual" creditor IDs internally, which they translate to "real"
creditor IDs before sending the messages to a particular accounting
authority node. (Also, the reverse translation should be applied for
incoming messages.)

## Account balances

The amount that is available on each SMP account is (again) represented by a
64-bit integer number. Using whole numbers eliminates rounding problems,
without having significant drawbacks, because a whole number can always be
divided by 100, 1000, or 10000000 before display.

[Let me remind you](/2022/07/08/interest-rates-in-swaptacular/) that the
issuer of each Swaptacular currency sets the interest rate for the currency.
When the interest rate is not zero (can be positive or negative), an
interest will constantly accumulate on currency holders' accounts. In this
case, the **account balance** will be the sum of the remaining *principal
balance*, and the accumulated *non-capitalized interest*.

## Creating and deleting accounts

To better understand the process of opening and closing SMP accounts,
imagine two peer Swaptacular nodes: a *creditors agent node*, and an
*accounting authority node*. In our imaginary scenario, one of the currency
holders which the creditors agent node represents (creditor ID = 789), wants
to open an account with some currency issuer (debtor ID = 123).

Here is the sequence of SMP messages that the two peer nodes would exchange,
so as to create the new account, and sometime later, to delete the account:

<div class="message">
  <img src="/images/smp-configure-account.svg"
       alt="Creating an account, and then deleting it">
</div>

TODO: Explain config_data, negligible_amount, scheduled_for_deletion.

<div class="message">
  <img src="/images/smp-commit-transfer.svg"
       alt="Creating an account, and then deleting it">
</div>

TODO: Explain coordinator_type, coordinator_request_id, account identifiers,
min/max_lock_amount, lock_amount, transfer_note, committed_amount,
acquired_amount.

TODO: Explain that debtors use the same protocol, but creditor_id is zero
for debtors' accounts.
