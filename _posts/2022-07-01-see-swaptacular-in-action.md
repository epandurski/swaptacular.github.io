---
layout: post
title: See Swaptacular in Action
description: Presents Swaptacular's "My wallet" and "My currency" apps
author: Evgeni Pandurski
tags: [intro]
---

After months of hard work on the [Currency Issuer
UI](https://github.com/epandurski/swpt_debtors_ui), and the [Currency
Holder UI](https://github.com/epandurski/swpt_creditors_ui), I am
happy to show something that works reasonably well (fingers crossed).

There are two apps: [My currency]({{ site.app_demo.debtors_webapp}})
and [My wallet]({{ site.app_demo.creditors_webapp}}). The fist app
allows you to create your own currency. The second app allows you to
use other peoples' currencies, including your own one. These are
progressive web-apps (PWA), which can be installed and can work
offline.

<!--more-->

For evaluation and testing, it is best to have 2 people, or at least 2
devices (a smartphone and a PC for example), running each of the apps
on a different device. This way, you can scan the QR codes generated
on the first device, with the second device.

At some point, *My wallet app* will ask you for a "digital coin" QR
code. You can generate one yourself, using the *My currency app*, or
you can scan the digital coin for my demo currency:

![Example digital coin](/images/example_digital_coin.png)

If you want to obtain some amount of my demo currency, send a comment
to this post, containing a link to your payment request. Also, once
you create your own currency, you will be able to issue any amount of
it to your wallet.

<div class="message">
  <p class="lead">Happy testing everyone! 🎉</p>
  <p>
    <b>Note:</b> <i>My wallet app</i> allows you to set an exchange policy
    for each currency in your wallet, so that the creditors agent could arrange
    circular exchanges with other users. This functionality is not
    implemented on the server yet. Currently, all exchange policies set
    by users will be ignored.
  </p>
</div>
