---
layout: page
title: Overview
---

Digital currencies are all around us. Nowadays, it is relatively easy
to become a holder of a digital currency, but it is not at all easy to
become an issuer of a digital currency. Swaptacular tries to make
creating and issuing new digital currencies practical for everyone.
The Swaptacular project consists of three things:

1. The network architecture.
2. A set of interoperability protocols.
3. Reference implementations for the interoperability protocols.


## The network architecture

In Swaptacular's network architecture, there are five types of nodes:

1. **Accounting Authorities** manage user account balances. They form
   the backbone of the network.
2. **Currency Issuers** create currencies and issue money into
   existence. In Swaptacular, they are also called *debtors*.
3. **Debtors Agents** are proxies that connect currency issuers to
   accounting authorities. They *may* also act as guarantors to debtors.
4. **Currency Holders** can make and receive payments. In Swaptacular, they
   are also called *creditors*.
5. **Creditors Agents** are proxies that connect currency holders to
   accounting authorities. They *may* also facilitate [automated
   currency exchanges between
   creditors](https://github.com/swaptacular/swpt_trade).

<div class="message">
  <img src="/images/swpt_basic_network.svg" alt="Swaptacular Basic Network">
</div>

The diagram above shows the simplest possible Swaptacular
network. Note that different network nodes (*accounting authorities,
creditors agents, debtors agents*) can be operated by different
organizations or individuals. Thus, very much like Internet,
**Swaptacular's network is decentralized by its nature**.

Another important thing to note is that one *creditors agent* can
connect *currency holders* to many different *accounting
authorities*. The following diagram illustrates the connections that
can exist between different types of nodes in a real-world network:

<div class="message">
  <img src="/images/swpt_complex_nework.svg" alt="Swaptacular Real-world Network">
</div>

The above diagram shows two different *creditors agents*, each
connecting *currency holders* to two different *accounting
authorities*. It also shows two different *debtors agents*, each
connecting *currency issuers* to the same accounting authority.


## Interoperability protocols

At the core of Swaptacular's network architecture is the [Swaptacular
Messaging Protocol](/public/docs/protocol.pdf), which governs the
communication between accounting authorities and debtors/creditors
agents. The protocol uses a [two-phase
commit](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)
schema for making payments, which allows for the implementation of
automatic currency exchanges in the spirit of [Circular Multilateral
Barter](/public/docs/cmb-general.pdf), eliminating the need for a
single reserve currency.

In order to allow currency holders to use a client application of
their choice, Swaptacular recommends creditors agents to follow the 
[Payments Web API Specification](/public/docs/swpt_creditors/redoc.html).

Since interchangeability of client applications for currency issuing is not
of critical importance, Swaptacular does not make recommendations about the
*Issuing Web API*. (The reference implementation uses [Simple Issuing Web
API](/public/docs/swpt_debtors/redoc.html).)

Swaptacular takes the interoperability between different
implementations very seriously, and tries to produce precise, clear,
and concise specifications for every important aspect of the system:

* [Swaptacular Messaging Protocol](/public/docs/protocol.pdf)
* [JSON Serialization for the Swaptacular Messaging
  Protocol](/public/docs/protocol-json.pdf)
* [STOMP Message Transport for the Swaptacular Messaging
  Protocol](/public/docs/swpt-stomp.pdf)
* [Swaptacular SSL/TLS Certificates](/public/docs/swpt-certificates.pdf)
* [RootConfigData JSON Documents](/public/docs/root-config-data.pdf)
* [The "swpt" URI Scheme](/public/docs/swpt-uri-scheme.pdf)
* [Digital Coins in Swaptacular](/public/docs/digital-coin-urls.pdf)
* [CoinInfo JSON Documents](/public/docs/coin-info-documents.pdf)
* [Payment Requests and Transfers in
  Swaptacular](/public/docs/payment-requests.pdf)
* [PR-zero Payment Request Documents](/public/docs/pr0-documents.pdf)
* [Payments Web API Specification](/public/docs/swpt_creditors/redoc.html)


## Reference implementations

* [Accounting Authority](https://github.com/swaptacular/swpt_accounts)
* [Debtors Agent](https://github.com/swaptacular/swpt_debtors)
* [Creditors Agent](https://github.com/swaptacular/swpt_creditors)
* [Circular Trade](https://github.com/swaptacular/swpt_trade)
* [Service that manages OAuth2 login and consent](https://github.com/swaptacular/swpt_login)
* [Currency Issuer UI](https://github.com/swaptacular/swpt_debtors_ui)
* [Currency Holder UI](https://github.com/swaptacular/swpt_creditors_ui)
* [Swaptacular Web API reverse proxy](https://github.com/swaptacular/swpt_apiproxy)
* [STOMP protocol client and server](https://github.com/swaptacular/swpt_stomp)
* [Managing Swaptacular certificate authorities](https://github.com/swaptacular/swpt_ca_scripts)

All the above implementations try to:

1. Be correct.
2. Be as simple as possible.
3. Be useful in the real world.
4. Demonstrate that an implementation that does scale very well
   horizontally, is indeed possible.

You can see examples how to run the different kinds of Swaptacular nodes
[here](https://github.com/swaptacular/swpt_accounts/blob/master/docker-compose-all.yml),
[here](https://github.com/swaptacular/swpt_creditors/blob/master/docker-compose-all.yml),
and
[here](https://github.com/swaptacular/swpt_debtors/blob/master/docker-compose-all.yml).


<div>
  <h2>Want to know more?</h2>
  <ul class="related-posts">
  {% for node in site.tags.intro %}
    {% unless node.tags contains 'overview' %}
      <li>
        <h3>
          <a href="{{ node.url }}">
            {{ node.title }}
          </a>
          <small>{{ node.date | date_to_string }}</small>
        </h3>
      </li>
    {% endunless %}
  {% endfor %}
  </ul>
</div>
