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

In the diagram above, there are several things that are worth mentioning:

* Every new account receives an account identifier (see the `account_id`
  text field). The *(debtor_id, account_id)* pair uniquely identifies each
  SMP account. To send money to a given account, the payer must know both
  the debtor ID, and the account identifier.

* The `config_data` text field allows the owner of the account to set
  additional configuration parameters on the account. Different accounting
  authority node implementations may support different configuration
  parameters.

* The `negligible_amount` field is used to decide whether an account can be
  safely deleted, and whether an incoming transfer is insignificant and can
  be ignored. The latter allows account owners to protect themselves from
  being spammed with lots of worthless incoming payments.

* The `scheduled_for_deletion` configuration flag indicates that the owner
  of the account wants to close the account. Note that when the remaining
  account balance is non-negligible, deleting the account will result in the
  loss of a potentially significant amount of money. For this reason, the
  deletion of the account will be postponed until the remaining account
  balance becomes negligible.

## Making and receiving payments

In our imaginary scenario, once the currency holder has received some amount
of money on his newly created account, he will want to make a payment to
someone else's account. The next diagram shows the messages that will be
exchanged in order to make this payment:

<div class="message">
  <img src="/images/smp-commit-transfer.svg"
       alt="Creating an account, and then deleting it">
</div>

Let me draw your attention to several important things in the above diagram:

* Every transfer is committed in two phases: First the transfer is
  *prepared*, and then the prepared transfer is *committed* (or
  alternatively, the prepared transfer could be *dismissed*).

* When preparing a transfer, the payer has the option to request some amount
  of money to be *locked* (that is: at least `min_locked_amount`, but no
  more than `max_locked_amount`). Locking the amount means that it will not
  be available for other transfers. Once the amount has been set aside, it
  is almost 100% certain that the future attempt to commit the locked
  amount, or any amount smaller than the locked amount, will be successful.

  Note that locking is entirely optional, and therefore the
  `committed_amount` can be equal, smaller, or bigger than the actual locked
  amount. The only purpose of locking is to guarantee a successful commit in
  the future. This guarantee allows several prepared transfers to be
  committed atomically (all or nothing).

* When a transfer has been successfully committed, both the sender an the
  recipient should receive an "AccountTransfer" message, informing them
  about the transaction. The `acquired_amount` for both will be the same,
  but for the sender it will be negative, while for the recipient it will be
  positive.

  However, if the acquired amount does not exceed the negligible amount
  configured by the recipient, an "AccountTransfer" message will not be sent
  to the recipient, protecting him/her from being spammed with lots of
  worthless incoming payments.

* The owner of one account is not the only entity that is able to initiate
  transfers from the account. Interest payments are a good example. When the
  interest rate is negative, interest payments must be made regularly from
  the owner's account, initiated by some automated system.

  Automated systems internal to the accounting authority node, which can
  initiate transfers on behalf of the account's owner, are called
  *coordinators*. In reality, the owner of the account is just a somewhat
  special "direct" type of coordinator. This fact is represented by the
  field `coordinator_type` in the exchanged SMP messages.

## Debtors' accounts

In the previous examples I explained how accounts are created, deleted, and
money transferred from one account to another. I did not explain, however,
how the amounts of money that we moved around went into existence in the
first place. If every account that a currency holder creates starts out with
no money in it, where the money originally comes from?

The answer is quite simple: **The money comes from the debtor's account.**

We saw how creditors agent nodes use the Swaptacular Messaging Protocol
(SMP) to communicate with accounting authority nodes. However, *debtors
agent nodes*, in exactly the same way, use SMP to communicate with the
accounting authority node which they are connected to. The only difference
is that each currency issuer (aka debtor) uses a special account called "the
debtor's account". The debtor’s account is special in the following ways:

* Every debtor has exactly one debtor's account. The creditor ID of each
  debtor's account is 0. (The creditor IDs of "normal" currency holder
  accounts can not be 0.)

* The balance on the debtor's account can go negative, thus allowing the
  debtor to create money into existence.

* Each debtor can use its debtor's account ``config_data`` text field, to
  [configure various important parameters of the
  currency](/public/docs/root-config-data.pdf) (like the interest rate).

## Conclusion

In this post I explained in broad strokes how the Swaptacular Messaging
Protocol works. I tried to demonstrate the most important use cases, but SMP
being a relatively complex protocol, a lot of use cases still remain in the
dark. You can check [the protocol specification](/public/docs/protocol.pdf)
for more details.

In further posts, I will talk about the [Payments Web API
Specification](/public/docs/swpt_creditors/redoc.html).
