---
layout: post
title:  "How we expect to use Compliance Tests"
cover-img: /assets/img/sign_splash.jpg
date:   2024-03-03 15:12:09 +0000
author: Dan Caseley
categories: 
---

One of XMPP's largest challenges (and indeed of all open standards) is interoperability. We want to enable anyone to use the standards on any platform they like, written in the programming language they prefer, focussing design and utility toward any group, from enterprise to egg-cup enthusiasts.

When we came up with our idea for XMPP interop tests for server implementations, we knew we weren't the first folk to ever have the idea. What was important for us wasn't about getting the tests right, but about getting the tests _used_. With so many programming languages in play, we can't get almost everyone to adopt an additional language into their toolchain. That's a clear no-go.

Instead, we're choosing to wrap our tests in the packaging of the common CI systems (a Github Action, a Circle CI Orb, etc) so that whatever your regular toolchain, you can slot the tests into the test phase of your build pipeline.

Previously you might've had something like:

```
------------    ---------    -------------    --------------------    --------------------
| Checkout | => | Build | => | Unit Test | => | Integration Test | => | Publish Artifact |
------------    ---------    -------------    --------------------    --------------------
```

You'd just slot this into the pipeline, like this:

```
------------    ---------    -------------    --------------------    --------------------    --------------------
| Checkout | => | Build | => | Unit Test | => | Integration Test | => | Compliance Tests | => | Publish Artifact |
------------    ---------    -------------    --------------------    --------------------    --------------------
```

Since the compliance tests are arms-length tests, akin to E2E tests, one individual test will likely exercise more code and run slower than the other kinds of test, so you'd naturally want to run the faster tests first.

Further, since it's an actual XMPP test, the implementing server author will also need to have a mechanism of running the XMPP server in the pipeline.

_Splash image courtesy of [Javier Allegue Barros, Unsplash](https://unsplash.com/photos/silhouette-of-road-signage-during-golden-hour-C7B-ExXpOIE?utm_content=creditShareLink&utm_medium=referral&utm_source=unsplash)_
