---
layout: page
title: Overview
---

Digital currencies are all around us. Nowadays, it is relatively easy
to become a holder of a digital currency, but it is not at all easy to
become an issuer of a digital currency. Swaptacular tries to make
creating and issuing new digital currencies possible for everyone. The
Swaptacular project consists of three things:

1. The network architecture
2. A set of interoperability protocols
3. Reference implementations for the interoperability protocols


## The network architecture

In Swaptacular's network architecture, there are five types of nodes:

1. **Accounting Authorities** manage user account balances. They form
   the backbone of the network.
2. **Currency Issuers** create currencies and issue money into
   existence. In Swaptacular, they are also called "debtors".
3. **Debtors Agents** are proxies that connect currency issuers to
   accounting authorities.
4. **Currency Holders** can make and receive payments. In Swaptacular,
   they are also called "creditors".
5. **Creditors Agents** are proxies that connect currency holders to
   accounting authorities.

<div class="message">
  <img src="/public/diagrams/swpt_basic_network.svg" alt="Swaptacular Basic Network">
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
  <img src="/public/diagrams/swpt_complex_nework.svg" alt="Swaptacular Real-world Network">
</div>

The above diagram shows two different *creditors agents*, each
connecting *currency holders* to two different *accounting
authorities*. It also shows two different *debtors agents*, each
connecting *currency issuers* to the same accounting authority.


## Interoperability protocols

At the core of Swaptacular's network architecture is the [Swaptacular
Messaging
Protocol](https://epandurski.github.io/swaptacular/protocol.pdf),
which governs the communication between accounting authorities and
debtors/creditors agents. The protocol uses a [two-phase
commit](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)
schema for making payments, which allows for the implementation of
automatic currency exchanges in the spirit of [Circular Multilateral
Barter](https://epandurski.github.io/swaptacular/cmb/cmb-general.pdf),
eliminating the need for a single reserve currency.

In order to allow currency holders to use a client application of
their choice, Swaptacular recommends creditors agents to follow the 
[Payments Web API Specification](https://epandurski.github.io/swaptacular/swpt_creditors/redoc.html).

Since interchangeability of client applications for currency issuing
is not of critical importance, Swaptacular does not make
recommendations about the *Issuing Web API*. The current reference
implementation uses a [Simple Issuing Web
API](https://epandurski.github.io/swaptacular/swpt_debtors/redoc.html).


## Reference implementations

* [Accounting Authority](https://github.com/epandurski/swpt_accounts)
* [Debtors Agent](https://github.com/epandurski/swpt_debtors)
* [Creditors Agent](https://github.com/epandurski/swpt_creditors)
* [Service that manages OAuth2 login and consent](https://github.com/epandurski/swpt_login)
* [Currency Issuer UI](https://github.com/epandurski/swpt_debtors_ui)
* [Currency Holder UI](https://github.com/epandurski/swpt_creditors_ui)

All the above implementations try to:

1. Be correct.
2. Be as simple as possible.
3. Be useful in the real world.
4. Demonstrate that an implementation that does scale very well
   horizontally, is indeed possible.


## Remaining work

- [x] Implement a user friendly UI for currency issuing.
- [x] Implement a user friendly UI for making and receiving payments.
- [ ] Define and implement a standard serialization for the messaging
  protocol.
- [ ] Allow creditors/debtors agents to easily connect to multiple
  accounting authorities, using user friendly UI and/or configuration
  files.
- [ ] Implement circular currency exchanges.


<div>
  <h2>Want to know more?</h2>
  <ul class="related-posts">
  {% for node in site.categories.intro %}
    {% unless node.categories contains 'overview' %}
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
