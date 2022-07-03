---
layout: post
title: What is a Currency Peg?
description: >
  Explains what is a currency peg, why Swaptacular uses currency pegs,
  and how banks, surprisingly, use them as well.
author: Evgeni Pandurski
tags: [intro]
---

<div class="lead">
  Allowing everybody to create and issue their own currency is a
  terrible idea, right? Wouldn't this turn into a complete mess?
</div>

## Well, yes, and no!

This is where currency pegging comes in:

<!--more-->

> Currency pegging is when a country attaches, or pegs, its exchange
> rate to another currency, or basket of currencies, or another
> measure of value, such as gold. Pegging is sometimes referred to as
> a fixed exchange rate. A currency peg is primarily used to provide
> stability to a currency by attaching its value, in a predetermined
> ratio, to a different and more stable currency.

Quite often, governments of smaller countries peg their currencies to
the currency of a bigger country. But this "pegging business" does not
stop here!

When a new commercial bank starts to operate, in reality, a brand new
digital currency is being created (that is: customers' accounts at the
bank). When the bank approves a loan, fresh new tokens of the bank's
digital currency are being issued in circulation. With tens of
thousands of commercial banks operating, how come this does not turn
into a complete mess?

You probably know the answer to this question already: Every
commercial bank pegs its digital currency to the currency issued by
the central bank of the country. The digital currency issued by the
central bank is called *bank reserves*.

> Bank reserves are the cash minimums that financial institutions must
> have on hand in order to meet central bank requirements. This is
> real paper money that must be kept by the bank in a vault on-site or
> held in its account at the central bank. Cash reserves requirements
> are intended to ensure that every bank can meet any large and
> unexpected demand for withdrawals.

When somebody transfers money from an account in one commercial bank,
to an account in another commercial bank, in reality, several
"invisible" exchanges happen:

1. The sender's bank "sells" some amount of its *bank reserves* to the
   central bank.

2. The central bank increases the *bank reserves* held by the
   recipient's bank, and instructs the bank to deposit the same amount
   of recipient's bank digital currency to the recipient's account.

3. If the *bank reserves* held by the sender's bank fall below the
   required minimum, the bank can sell some of its assets (bonds,
   mortgages, etc.) to the central bank, and in exchange, obtain the
   needed *bank reserves*.

So, on closer inspection, one realizes that what looks like a single
physical (paper) currency, in reality, is a tree of digital currencies
held together by currency pegs:

<div class="message" id="peg-tree">
  <img src="/images/bank-pegs-tree.svg" alt="A tree of currency pegs">
</div>

The above tree of currency pegs shows only issuers of digital
currencies that are regulated by the banking law. But again, the
“pegging business” does not stop here!

Every non-banking institution that issues
[IOUs](https://en.wikipedia.org/wiki/IOU), which can be traded on some
market, in reality, issues a currency (digital or not). Most of these
non-banking currencies are also pegged to the national currency.

[Bonds](https://en.wikipedia.org/wiki/Bond_(finance)) are the most
obvious example. Bonds can be issued by governments, municipalities,
or corporations. They are traded on specialized markets, where most of
the buyers and sellers are banks, or other big financial
institutions. Every bond has a [face
value](https://en.wikipedia.org/wiki/Face_value), which is a fixed
exchange rate to the national currency, declared by the issuer of the
bond. In theory, the amount that bond issuers can issue is
unlimited. In practice, the amount is limited by the availability of
bond buyers.

Now, getting back to the original question:

<div class="message">
  Isn't allowing everybody to create and issue their own currency a
  terrible idea?
</div>

Well, I do not know! I guess history will be the judge. But what I
know is that lots of big, and not so big players, have been doing this
for hundreds for years, and the "game" is much less regulated than
commonly perceived. Occasionally, this turns into to a mess. But most
of the time, it seems to work quite well.

## Currency Pegging in Swaptacular

Currency pegging is a first-class citizen in Swaptacular. When you
configure your own currency, you can specify a fixed exchange rate
with some other Swaptacular currency. Non-Swaptacular currencies (USD,
EUR, gold etc.) can also be used as pegs, if global currency IDs have
been reserved for them.

In practice, this means that almost all of the currencies created in
Swaptacular will be denominated in well known currency units (USD,
EUR, gold), and will not even have their own name, apart from the name
of the issuer. Precisely for this reason, in Swaptacular currency
issuers are called *debtors*, and currency holders are called
*creditors*.

## Currency Exchanges in Swaptacular

Remember the example I gave earlier, when somebody wanted to transfer
money from an account in one commercial bank, to an account in another
commercial bank? It turned out, because every commercial bank, in
reality, issues its own digital currency, the amount first had to be
exchanged to *bank reserves*. (That is, the digital currency issued by
the central bank.)

Well, it is nearly the same in Swaptacular! You can use a currency
that you have, and swap it with a currency that you need. Remember, as
long as both currencies are part of the same [tree of currency
pegs](#peg-tree), such an exchange can be performed more or less
automatically, because the exchange rate is fixed and well known.

In practice, performing automatic exchanges between gazillion of
currencies is not an easy task. One proven strategy is to have
specialized *currency exchange hubs*, which operate for
profit. Another promising strategy is to implement automatic currency
exchanges in the spirit of [Circular Multilateral
Barter](https://epandurski.github.io/swaptacular/cmb/cmb-general.pdf).

Allowing automatic currency exchanges in Swaptacular was a key goal
since the beginning. All the underlying protocols are designed to
support it, and I hope that in the not so distant future, we will have
the time and resources to implement it.

<div class="message">
  <b>Note:</b> The <a href="{{ site.app_demo.creditors_webapp}}">Currency
  Holder UI</a> allows you to set an exchange policy for each currency
  in your wallet, so that the creditors agent could arrange circular
  exchanges with other users. This functionality is not implemented on
  the server yet. Currently, all exchange policies set by users will
  be ignored.
</div>
