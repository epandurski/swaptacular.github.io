---
layout: page
title: Run your own node
order_number: 5
---

Like the Internet, Swaptacular is decentralized by nature. Companies
and individuals can run their own Swaptacular servers and connect them
to servers operated by others. [There are several types of
nodes](/overview/), each serving a different role in the network:

* creditors agent nodes
* accounting authority nodes
* debtors agent nodes

<div class="lead message">
  👉 If you are considering running your own Swaptacular node, feel
  free to <a href="/contact/">contact us.</a>
</div>

If you have decided to run your own Swaptacular node, there are
several ways to do so. The best approach depends on your goals. Below
are the most common scenarios, listed from the simplest to the most
advanced:


## Let someone else run a creditors agent node for you

Running your own creditors agent node allows you [to offer automated
currency exchanges to your
users](/2024/07/04/automated-currency-exchanges/). Most importantly,
it enables you to collect a small fee from each completed exchange.

The easiest way to get started is to let
[swaptacular.org](https://www.swaptacular.org/) run the node for you.
We are committed to keeping this service free, and we rely on
donations to support it.

<p>{% include sponsor_button.html %}</p>


## Run a creditors agent node yourself

If you prefer not to depend on a third party, you can [deploy a
creditors agent node on your own Kubernetes
cluster](/2025/09/18/ready-for-production/). We strive to make this
option as simple and straightforward as possible.

Once your creditors agent node is up and running, you should [connect
it](/2023/04/26/under-the-hood-peer-connections/) to at least one
[accounting authority node from the public
list](#public-list-of-accounting-authority-nodes).


**Note:** To use a well-known currency as a peg, check [the list of
reserved currency IDs](#reserved-currency-ids) first. If the currency
you want to use is not listed, contact us to have it added.


## Run an accounting authority node

Running your own accounting authority node helps ensure the long-term
reliability of the digital currencies it manages. The recommended
approach is to [deploy it on your own Kubernetes
cluster](/2025/09/18/ready-for-production/). In most cases, you will
also want to run a creditors agent node and a debtors agent node,
either on the same cluster or on separate ones.

Once your accounting authority node is operational, please contact us
so we can add it to the [public list of accounting authority
nodes](#public-list-of-accounting-authority-nodes). This will allow
others to discover your node and — subject to your approval — [connect
to it.](/2023/04/26/under-the-hood-peer-connections/)

**Note:** To create a root SSL certificate for your accounting
authority node, you must first obtain a unique *accounting authority
prefix*. The currently recommended method is to randomly select a
hexadecimal number between `0x10000000` and `0x1000ffff` that does not
match any existing *serialNumber* in the public list of accounting
authority nodes.


## Public list of accounting authority nodes

<table>
  <thead>
    <tr>
      <th>Node Name</th>
      <th>serial&#8203;Number</th>
      <th>Added</th>
      <th>Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="/public/infobundles/aa_swaptacular_org.zip">aa.swaptacular.org</a></td>
      <td><code>10000000</code></td>
      <td>2026-04-12</td>
      <td>operational</td>
    </tr>
  </tbody>
</table>

**Note:** Click on the name of the node to download its [info-bundle
file.](/2023/08/03/under-the-hood-message-transport/#info-bundle-files)

## Reserved currency IDs

<table>
  <thead>
    <tr>
      <th>Currency ID / Debtor ID</th>
      <th>Currency Name</th>
      <th>Digital Coin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>840</td>
      <td>United States Dollar</td>
      <td><a href="https://debtors.swaptacular.org/840#swpt:840" target="_blank" style="word-break: break-all">https://debtors.swaptacular.org/840#swpt:840</a></td>
    </tr>
    <tr>
      <td>978</td>
      <td>Euro</td>
      <td><a href="https://debtors.swaptacular.org/978#swpt:978" target="_blank" style="word-break: break-all">https://debtors.swaptacular.org/978#swpt:978</a></td>
    </tr>
  </tbody>
</table>

