---
layout: post
title: The First Production Nodes Are Online!
description: >
  Announces that the first production Swaptacular nodes have joined
  the network.
author: Evgeni Pandurski
tags: [intro]
---

I am happy to announce that the first production Swaptacular nodes
have joined the network: **aa.swaptacular.org**,
**da0.swaptacular.org**, and **euroscrip.net**!

In [Ready for Production!](/2025/09/18/ready-for-production/), I
announced that Swaptacular nodes could be deployed to Kubernetes
clusters using a ready-made configuration template. Now we have
production nodes covering the three server roles needed to issue,
hold, and exchange currencies.

<!--more-->

## What do the three nodes do?

Each node has a different role:

* **aa.swaptacular.org** is an *accounting authority*. It maintains
  account balances and processes transfers.
* **da0.swaptacular.org** is a *debtors agent*. It connects currency
  issuers to the accounting authority. Issuers interact with it
  through the [My Currency app]({{ site.app_demo.debtors_webapp }}).
* **euroscrip.net** is a *creditors agent*. It connects currency
  holders to the accounting authority and provides automated currency
  exchanges. Currency holders interact with it through the
  [My Wallet app]({{ site.app_demo.creditors_webapp }}).

Together, these nodes provide the infrastructure for someone to
create a currency, issue it to other people, and for those people to
hold and transfer it. Currency holders can also set exchange policies
to participate in automated exchanges with other currencies.

If you would like to try the apps, the [Show Me!](/show-me/) page
walks through creating a currency and opening an account to hold it.
The two apps serve different purposes, so you will need a separate
registration in each if you want to try both sides.

## A beginning for the network

These first nodes are a starting point. Swaptacular is designed to
connect nodes operated by different organizations, using open
protocols. More operators can join the network and offer services to
their own communities.

If you would like to run a node, the [Run a Node](/run-node/) guide
explains the available roles and links to the deployment
instructions. You are also welcome to [get in touch](/contact/) if
you would like to discuss connecting your node to the network.

Whether you want to create a currency, use one, or run a node, you are
welcome to join.

