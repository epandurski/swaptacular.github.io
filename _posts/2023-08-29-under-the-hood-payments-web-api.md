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

In this section I will outline the different types of objects that exist in
the API, and the role of each object type.

**Important note:** Many of the object types contain fields which values are
64-bit integers. While big integers are perfectly valid JSON, the standard
[EcmaScript](https://en.wikipedia.org/wiki/ECMAScript) JSON parser and
serializer do not work correctly with big integers.

### `PaginatedList` objects

Quite a few of the endpoints in the API return a potentially very long list
of items. In these cases, the "PaginatedList" object type is used:

<div class="message">
  <img src="/images/paginated-list.svg"
       alt="Shows a paginated list of items">
</div>

### `PaginatedStream` objects

Sometimes we have a list of items which grows continuously (a list of log
entries, for example). In these cases, it is important to allow the client
to immediately skip to the end of the list, and then to periodically check
for new items. For this, the "PaginatedStream" object type is used:

<div class="message">
  <img src="/images/paginated-stream.svg"
       alt="Shows a paginated stream of items">
</div>

### `LogEntry` objects

Client applications should be able to efficiently synchronize their local
databases with the server's database, even after long disconnected periods.
For this reason, the API keeps a log of each update to every important
object (that is: accounts, incoming transfers, outgoing transfers etc.).

The log is a `PaginatedStream` of `LogEntry` objects. The stream contains
all historical changes to all objects belonging to a given currency holder.

Every updatable object in the API has a *"latestUpdateId"* field. Each time
an object is updated, the object's "latestUpdateId" gets incremented, and a
log entry is added to the log, referring to the updated object and its new
"latestUpdateId" value:

<div class="message">
  <img src="/images/log-entry-example.svg"
       alt="Shows an example log entry">
</div>

The picture above shows a `LogEntry` object, added to the log to inform
about the successful finalization of some transfer (and the update of the
corresponding "Transfer" object). The log entry tells the client that the
transfer object at *"/example-transfer"* has been updated, and the new
"version" of the object is "2". After processing this log entry, the client
may decide to make an additional HTTP request to *"/example-transfer"*, so
as to obtain the new state of the transfer.

Quite frequently, the client will already have the latest version of the
updated object in its local database, so that no additional HTTP request
will be needed. In such cases, the value of log entry's *"objectUpdateId"*
field will be smaller or equal to the value of the *"latestUpdateId"* field
from the object in the client's local database.

It is important to mention that for most types of updated objects, a "data"
field will not be present in the corresponding log entry. However, because
`Transfer` object updates are quite common, the provided "data" field allows
the client to never perform an additional HTTP request (the request to
obtain the new state of the transfer from *"/example-transfer"*), inferring
the new state from the supplied "data". The "data" field is just a nice
little optimization.

### `PinInfo`objects

The API provides *the option* every potentially dangerous operation to be
protected by a PIN (Personal Identification Number). This can be especially
useful then the client of the API is a mobile app. In this case, for
convenience reasons, the user is likely to stay logged in for long periods
of time, during which other people may get physical access to the user's
device.

`PinInfo` objects represent the user's PIN, and its current status ("on",
"off", or "blocked").

### `AccountList` objects

The API maintains a list of accounts (an "AccountList" object) for every
currency holder. The list of accounts is simply a `PaginatedList` of
references (URIs) to all `Account` objects which the currency holder owns.

### `Account` objects

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

### Swagger UI

Check the [Creditors Agent Swagger
  UI](https://demo.swaptacular.org/creditors-swagger-ui/) (client_id:
  `swagger-ui`, client_secret: `swagger-ui`)
