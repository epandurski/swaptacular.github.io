---
layout: post
title: Under the Hood — Payments Web API
description: >
  Explains the basics of Swaptacular's Payments Web API.
author: Evgeni Pandurski
tags: [under-the-hood]
published: false
---

In [a previous post](/2023/08/28/under-the-hood-smp/) I explained how the
Swaptacular Messaging Protocol works, demonstrating the most important use
cases.

In this post I will talk about the [server Web
API](https://en.wikipedia.org/wiki/Web_API) that currency holders' client
apps use, to directly communicate with the [creditors agent
node](/overview/) which is responsible for managing their accounts.

<!--more-->

Most currency holders (aka creditors) would want to use the services of more
than one creditors agent, and because of this, the interoperability between
different currency holder client apps, and different creditors agent servers
is very important. To facilitate this interoperability, Swaptacular
recommends every creditors agent to implement the [Payments Web API
Specification](/public/docs/swpt_creditors/redoc.html).

The **Payments Web API** (PWAPI for short) is a practical [RESTful
API](https://en.wikipedia.org/wiki/Representational_state_transfer), defined
in the terms of the [OpenAPI](https://www.openapis.org/) specification
language. The PWAPI specification itself, contains a lot of details and
examples already, and therefore, here I will try to outline only the most
important high-level concepts, which can help the reader to navigate the
details.

**A fair warning:** This post may become too technical for the taste of some
readers.

## The scope of the API

TODO:

The creditors agent node acts as a proxy between the currency holder and the
accounting authority. The PWAPI mostly allows the client to do what the
Swaptacular Messaging Protocol does, but from a mobile device.

## Authentication and scalability

TODO:

- Oauth2
- Shards, [API Reverse Proxy](https://github.com/swaptacular/swpt_apiproxy)

## The "admin" module

TODO:

- Creditor ID reservation
- Creditor activation
- Creditor deactivation

## Client database synchronization, multiple clients

TODO:

- Synchronization the client database and the server database. The `log`.
- Synchronization between two or more clients

## The creditor's "wallet"

TODO:

The wallet is the gateway to all objects and operations in the API. This
follows the general REST principle, that client apps should not try to
assemble URI themselves, but they should use the URIs provided by the
server.

## Debtor identity and account identity

TODO:

Explain the [`swpt` URI scheme](/public/docs/swpt-uri-scheme.pdf).

## API object types

Explain how object updates work, and why the `latestUpdateId` is necessary
to guarantee database consistency.

### `PaginatedList` objects

TODO

### `PaginatedStream` objects

TODO

### `CreditorsList`, `Creditor` and `PinInfo`objects

TODO

### `AccountList` and `Account` objects

TODO:

Explain how account sub-objects work.

- `AccountInfo`, `DebtorInfo`
- `AccountConfig`
- `AccountDisplay`
- `AccountExchange`
- `AccountKnowledge`
- `AccountLedger`, `LedgerEntry`, `CommittedTransfer`

### `TransfersList` and `Transfer` objects

TODO

### `LogEntry` objects

TODO:

Give an example explaining relation between the log entry and the updated
object `Transfer` object.
