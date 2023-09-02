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

In this post I will talk about the server Web API that currency holders'
client applications use, to communicate with the [creditors agent
nodes](/overview/).

<!--more-->

Many currency holders (aka creditors) would need to use the services of more
than one creditors agent nodes. For this reason, interoperability between
different currency holder client applications, and different creditors agent
servers is very important. To facilitate this interoperability, Swaptacular
recommends every creditors agent node to implement the [Payments Web API
Specification](/public/docs/swpt_creditors/redoc.html).

The **Payments Web API** which we will discuss here, is a practical [RESTful
API](https://en.wikipedia.org/wiki/Representational_state_transfer), defined
in the terms of the [OpenAPI](https://www.openapis.org/) specification
language. The API specification itself contains lots of details and examples
already, and therefore, here I will try to outline only the most important
high-level concepts, which can help the reader to navigate the details.

**A fair warning:** This post may become too technical for the taste of some
readers.

## API design goals

In essence, every creditors agent node acts as a proxy between currency
holders and accounting authority nodes. Thus, the main purpose of the
Payments Web API is to allow client applications to preform the same
operations that the Swaptacular Messaging Protocol allows, but using a
synchronous Web API, instead of an asynchronous messaging protocol.

Another important objective is to allow currency holders to declare *simple
currency exchange policies*. Ultimately, currency holders should be able to
effortlessly exchange currencies that they have, but do not need, for
currencies that they need.

I should mention that the client applications which the API is intended for,
are not limited to simple mobile apps. A client application, for example,
could be a corporate accounting server, maintaining a huge database of
customers and invoices, and processing hundreds of automated transfers per
second.

Therefore, one of the main design goals for the Payments Web API is to allow
the efficient synchronization between the client's local database
(containing account balances, incoming and outgoing transfers, etc.) and the
master database, which resides on the creditors agent node.

Another important design goal is to allow several client applications (for
example, several mobile devices which the currency holder owns), to work
simultaneously without stepping on each others toes.

Lastly, pure Web apps should be able use the API, so that if need be, the
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

The API is designed so as to allow the decision to which database shard to
send each incoming request, to be taken as early as possible — simply by
looking at the request's URL. For example, to do load balancing, our
reference implementation uses a simple [API Reverse
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
  the reservation expires, and the reserved creditor ID is released.

- **Removing existing creditors (deactivation)**

  Activated creditors can be *deactivated*. A deactivated creditor can not
  perform any actions in the API, but its creditor ID remains unavailable
  for a lengthy period of time (5 years for example).

- **Obtaining the list of active creditors**

  External services can also obtain the list of all active creditors in the
  system. This gives external services a "starting point" to synchronize
  their databases with the main creditor's database.

## Creditors' wallets

In the API, every activated creditor receives a "wallet". The creditor's
wallet is an object which mostly contains links to other objects belonging
to the creditor (accounts, initiated transfers etc.). You may think of the
wallet object as a gateway to all the operations available in the API. The
design of the `Wallet` object follows the general principle that client
application should not try to assemble
[URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)
themselves. Instead, all the necessary URIs should be provided by the
server.

## API object types

In this section I will try to outline the most important types of objects in
the API, and the functions that they perform.

**Important note:** Some of the objects in the API contain fields whose
values are 64-bit integers. While big integers are perfectly valid in JSON,
the standard JavaScript [JSON
parser](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)
and
[serializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
do not work correctly with big integers. Therefore in this case, a more
capable JSON parser will be needed.

### `DebtorIdentity` and `AccountIdentity` objects

All Swaptacular currencies (aka debtors), and all currency holders' accounts
(aka creditors accounts, or simply *accounts*) are uniquely identified by
URIs. These URIs conform to the custom `swpt:` URI scheme. The syntax for
the "swpt" URI scheme is [specified here](/public/docs/swpt-uri-scheme.pdf).

In the API, in order to create an account with a given debtor, the currency
holder should provide a `DebtorIdentity` object. Here is an example
"DebtorIdentity" object:

{% highlight json %}
{
  "type": "DebtorIdentity",
  "uri": "swpt:1234"
}
{% endhighlight %}

Once an account has been created, to initiate a transfer, the currency
holder should provide the `AccountIdentity` object representing the
recipient's account: Here is an example "AccountIdentity" object:

{% highlight json %}
{
  "type": "AccountIdentity",
  "uri": "swpt:1234/recipient-account"
}
{% endhighlight %}

These two simple types of objects are used in quite a lot of places in the
API.

### `PaginatedList` objects

Quite a few of the endpoints in the API return a potentially very long list
of items. For these cases, the "PaginatedList" object type is used:

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
for the object in the client's local database.

It is important to mention that for most types of updated objects, a "data"
field will not be present in the corresponding log entry. However, because
`Transfer` object updates are quite common, the provided "data" field allows
the client to never perform an additional HTTP request (the request to
obtain the new state of the transfer at *"/example-transfer"*), inferring
the new state from the supplied "data".

All in all, when the "data" field is provided, this is only a performance
optimization.

### `PinInfo`objects

The API provides *the option* every potentially dangerous operation
(initiating transfers for example) to be protected by a PIN (Personal
Identification Number). This is especially useful then the client of the API
is a mobile app. In this case, for convenience reasons, the user is likely
to stay logged in for long periods of time, during which other people may
accidentally get physical access to the user's device.

`PinInfo` objects represent the user's PIN, and its status ("on", "off", or
"blocked"). The "blocked" status indicates that a wrong PIN has been tried
too many times, and a new PIN must be configured.

### `AccountList` objects

The API maintains a list of accounts (an "AccountList" object) for every
currency holder. The list of accounts is simply a `PaginatedList` of
references (URIs) to the `Account` objects which the currency holder owns.

### `Account` objects

For each created account, an new `Account` object is added to the currency
holder's list of accounts. Also, the currency holder can decide to schedule
some of his/her accounts for deletion, or to even delete them right away.

Every "Account" object contains several sub-objects. Each sub-object is
responsible for a different aspect of the account's behavior. Here is an
example "Account" object, with its sub-objects:

{% highlight json %}
{
  "type": "Account",
  "uri": "/creditors/1/accounts/1/",
  "createdAt":"2023-08-06T15:00:00Z",
  "debtor": {
      "type": "DebtorIdentity",
      "uri": "swpt:1234"
  },
  "accountsList": {
      "uri": "/creditors/1/accounts-list"
  },
  "latestUpdateId": 1,
  "latestUpdateAt": "2023-08-06T15:00:00Z",
  "ledger": {
    "type": "AccountLedger",
    "uri": "/creditors/1/accounts/1/ledger",
    ...
    This is the `AccountLedger` sub-object.
    ...
  },
  "info": {
    "type": "AccountInfo",
    "uri": "/creditors/1/accounts/1/info"
    ...
    This is the `AccountInfo` sub-object.
    ...
  },
  "config": {
    "type": "AccountConfig",
    "uri": "/creditors/1/accounts/1/config",
    ...
    This is the `AccountConfig` sub-object.
    ...
  },
  "display": {
    "type": "AccountDisplay",
    "uri": "/creditors/1/accounts/1/display"
    ...
    This is the `AccountDisplay` sub-object.
    ...
  },
  "exchange": {
    "type": "AccountExchange",
    "uri": "/creditors/1/accounts/1/exchange"
    ...
    This is the `AccountExchange` sub-object.
    ...
  },
  "knowledge": {
    "type": "AccountKnowledge",
    "uri": "/creditors/1/accounts/1/knowledge"
    ...
    This is the `AccountKnowledge` sub-object.
    ...
  }
}
{% endhighlight %}

**Important note:** Every sub-object has its own URI, and can be updated
independently from the parent `Account` object, and from the other
sub-objects. Also, updates in the different sub-objects are tracked
separately in the log.

#### `AccountLedger` sub-objects

"AccountLedger" sub-objects contain the current account balance, and a
`PaginatedList` of `LedgerEntry` objects. Each "LedgerEntry" object
describes an outgoing or an incoming transfer to the account. Outgoing and
incoming transfers are represented by `CommittedTransfer` objects.

#### `AccountInfo` sub-objects

"AccountInfo" sub-objects contain important technical information about the
account. For example: the account identity, the interest rate on the
account, possible configuration errors etc. The "AccountInfo" sub-object may
also include a `DebtorInfo` object, which essentially is *a reliable link*
to a [document containing additional information about the
debtor](/public/docs/coin-info-documents.pdf).

#### `AccountConfig` sub-objects

"AccountConfig" sub-objects allow currency holders to alter various
configuration settings on their accounts. Notably, they can schedule the
account for deletion, and alter the account's *"negligible amount"*.

#### `AccountDisplay` sub-objects

"AccountDisplay" sub-objects allow currency holders to alter the display
parameters of their currencies. Like the name of the currency, and the way
currency amounts are displayed.

#### `AccountExchange` sub-objects

"AccountExchange" sub-objects allow currency holders to declare their
currency exchange policies. To that end, the currency holder can declare a
fixed exchange rate between the tokens of two of his/her accounts (the
pegged currency, and the peg currency). To specify this relation, a
`CurrencyPeg` object is used.

#### `AccountKnowledge` sub-objects

"AccountKnowledge" sub-objects allow currency holders to store all sorts of
important data about the accounts, on the server.

For example: The client application can use the account's "AccountKnowledge"
sub-object to store the account's interest rate, which the currency holder
already knows about, and later, compare the stored value with the current
interest rate on the account. This way, a change in the interest rate will
be correctly detected, even when the currency holder uses several different
client devices (or applications).

### `TransfersList` objects

The API maintains a list of initiated transfers (an "TransfersList" object)
for every currency holder. The list of initiated transfers is simply a
`PaginatedList` of references (URIs) to the `Transfer` objects which the
currency holder owns.

### `Transfer` objects

To initiate a transfer to someone else's account, the currency holder
creates a "Transfer" object. Right after its creation, the transfer object
is in a *pending* state, which means that the transfer has been initiated,
and is waiting to be **finalized**. The finalization can be *successful*
(the amount has been successfully transferred), or *unsuccessful*.

Once the transfer has been finalized, and the client application has
informed the currency holder about the outcome, the "Transfer" object is not
needed anymore, and can be deleted. However, as a safety measure, and to
allow the finalized transfer to show up on other devices that the currency
holder may use, it is recommended not to delete the "Transfer" objects for
at least 5 days.

It is worth mentioning that the API allows a *cancellation* to be attempted
for pending transfers. The cancellation attempt may fail, in which case the
client has no other option but to wait for the transfer to be finalized
(successfully or unsuccessfully).

## Conclusion

In this post I explained the most important high-level concepts in the
[Payments Web API Specification](/public/docs/swpt_creditors/redoc.html).
You can use the convenient [Swagger
UI](https://demo.swaptacular.org/creditors-swagger-ui/) (client_id:
`swagger-ui`, client_secret: `swagger-ui`) to browse the documentation, to
and experiment with the API.

In further posts, I will talk about Swaptacular's "digital coins" and
payment requests.
