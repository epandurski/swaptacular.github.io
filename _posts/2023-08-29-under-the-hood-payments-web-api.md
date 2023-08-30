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

## API design principles

In essence, every creditors agent node acts as a proxy between currency
holders and accounting authority nodes. Thus, the main purpose of the
Payments Web API is to allow client applications to preform the same
operations that the Swaptacular Messaging Protocol allows, but using a
synchronous Web API, instead of an asynchronous message protocol.

It is important to mention that PWAPI client applications are not restricted
to simple mobile apps. A client application, for example, can be corporate
accounting server, maintaining a database of customers and invoices, and
processing hundreds of automated transfers per second.

Therefore, one of the main design goals for the Payments Web API is to allow
efficient (in terms of network throughput) synchronization between the
client's database (containing account balances, incoming and outgoing
transfers, etc.) and the creditors agent's database.

Another important design goal is to allow several client applications (for
example, several mobile devices which the currency holder owns), to work
simultaneously without stepping on each others toes.

Lastly, pure Web apps (that is: JavaScript apps running on a standard
browser) should be able to work with the API, do that if need be, the
currency holder can use someone else's computer to access his/hers accounts.

## Authentication

The API relies on [OAuth 2.0](https://oauth.net/2/) to authenticate the
users of the API. This gives creditors agents complete freedom to choose the
authentication scheme that is most appropriate in their context.

## Scalability

When the creditors agent has lots of users, the number of network requests
per second to the API can become too high for a single database server to
handle. Therefore, some form of [database
sharding](https://en.wikipedia.org/wiki/Shard_(database_architecture)) would
be needed.

PWAPI is designed so as to allow the decision to which database shard to
send each incoming request, to be taken as early as possible — simply by
looking at the request's URL. For example, to send each HTTP request to the
correct database shard (aka "load balancing"), the reference implementation
uses a simple [API Reverse
Proxy](https://github.com/swaptacular/swpt_apiproxy) HTTP server.

## The "admin" endpoints

Because the API relies on external services (OAuth 2.0 servers) for user
registration and user authentication, some "admin" endpoints must be
available in the API, to allow external services to create new currency
holders (aka "creditors"), and to remove existing creditors.

- **Creating new creditors (activation)**

  Often the external service will want to know the ID of the new creditor in
  advance, without actually creating the creditor. For this reason, the
  creation of new creditors can optionally be done in two-phases: First, a
  creditor ID is *reserved*, and only then, the creditor is *activated*. If
  the reserved creditor ID has not been activated within some period time,
  the reservation expires, and the creditor ID is released.

- **Removing existing creditors (deactivation)**

  Activated creditors can be *deactivated*. A deactivated creditor can not
  perform any actions in the API, but its creditor ID remain unavailable for
  some lengthy period of time (5 years for example).

- **Obtaining the list of active creditors**

  External services are also able to obtain the list of all active creditors
  in the system. This gives external services a "starting point" to
  synchronize their databases with the main creditor's database.

## Creditors' wallets

In the API, every activated creditor receives a "wallet". The creditor's
wallet is an object which mostly contains links to other objects belonging
to the creditor (accounts, initiated transfers etc.). You may think of the
wallet object as a gateway to all the other objects and operations in the
API. The design of the `Wallet` object follows the general REST principle,
that client application should not try to assemble
[URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) (links)
themselves, but all the necessary URIs should be provided by the server.

## Debtor and account identities

All Swaptacular currencies (aka debtors), and all currency holders accounts
(aka creditors accounts) are uniquely identified by URIs. These URIs conform
to the custom "swpt" URI scheme. The syntax for the `swpt:` URI scheme is
[specified here](/public/docs/swpt-uri-scheme.pdf).

In the API, to create an account with a given currency, the currency holder
should provide a `DebtorIdentity` object. Here is an example debtor identity
object:

{% highlight json %}
{
  "type": "DebtorIdentity",
  "uri": "swpt:1234"
}
{% endhighlight %}

To initiate a transfer, the currency holder should provide the
`AccountIdentity` object representing the recipient's account: Here is an
example account identity object:

{% highlight json %}
{
  "type": "AccountIdentity",
  "uri": "swpt:1234/recipient-account"
}
{% endhighlight %}

## API object types

Warn about the handling of 64-bit integers by the standard Javascript parser
and serializer.

### `PaginatedList` objects

TODO

### `PaginatedStream` objects

TODO

### `LogEntry` objects

TODO:

Give an example explaining relation between the log entry and the updated
object `Transfer` object.

Explain how object updates work, and why incrementing the `latestUpdateId`
is necessary to guarantee database consistency.

### `CreditorsList`, `Creditor` and `PinInfo`objects

TODO

### `AccountList` and `Account` objects

TODO:

Explain how the different account sub-objects work.

- `AccountInfo`, `DebtorInfo`
- `AccountConfig`
- `AccountDisplay`
- `AccountExchange`
- `AccountKnowledge`
- `AccountLedger`, `LedgerEntry`, `CommittedTransfer`

### `TransfersList` and `Transfer` objects

TODO
