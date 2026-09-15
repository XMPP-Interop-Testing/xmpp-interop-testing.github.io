---
layout: post
title: "Next-Gen Connectivity"
cover-img: /assets/img/next-gen-connectivity_splash.jpg
date: 2026-09-15 23:33:12 +0200
author: Guus der Kinderen
categories:
---

Earlier today, version 1.8.0 has been released. This release adds tests for exciting new XMPP functionality!

Classic XMPP connection setup takes a lot of round-trips: authenticate, bind a resource, enable carbons, kick off
stream management, sync the archive. Each one is its own request. Fine on wifi, annoying on a shaky mobile connection.

XMPP offers various XEPs to fix that. [SASL2 (XEP-0388)](https://xmpp.org/extensions/xep-0388.html) turns authentication
into a single extensible envelope instead of a fixed sequence of steps, and everything else here builds on it.
[Bind 2 (XEP-0386)](https://xmpp.org/extensions/xep-0386.html) folds resource binding and feature enablement into that
same envelope, so a client can walk away with a fully set-up session in (almost) one shot. And
[FAST (XEP-0484)](https://xmpp.org/extensions/xep-0484.html) lets a client swap its password for a short-lived, rotating
token and authenticate in a single round-trip, no SCRAM handshake required.

Put together, a client can go from opening a socket to fully authenticated and ready to send in essentially one
round-trip. That's also exactly the kind of multi-XEP choreography where servers tend to disagree on the details, so
we're glad to have it covered.

Give the new tests a try, and let us know what you find!

_Splash image courtesy of [Conny Schneider, Unsplash](https://unsplash.com/photos/a-blue-abstract-background-with-lines-and-dots-pREq0ns_p_E?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_
