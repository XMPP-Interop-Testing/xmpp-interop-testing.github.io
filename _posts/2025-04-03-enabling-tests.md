---
layout: post
title:  "Enabling Tests"
cover-img: /assets/img/switches_splash.jpg
date:   2025-04-03 13:04:02 +0100
author: Guus der Kinderen
categories:
---

Our project creates a framework that allows anyone to easily add XMPP standards compliance tests to the test phase of
their build pipeline. Prior to our most recent release (version 1.5.0) a test execution would basically run _all_ tests
in the test suite. We provided an option to exclude certain tests, but in essence, the bulk of tests would execute.

This behavior is generally preferable when testing an XMPP server implementation. A benefit of exclusion-based
configuration is that tests that are newly added to the test suite will automatically be picked up, without requiring a
configuration change.

However, there are scenarios where it is desirable to execute only a specific set of tests, for example when:
- testing of a server-sided component, that implements only one specification, or
- testing a development branch in which changes are applied to only one feature.

In those scenarios, having to disable _all other_ tests is cumbersome.

We have now made available a mechanism in which specific tests can be _included_. When you include tests, only the 
included tests are executed. These configuration is very similar to that of the exclusion of tests. You can find more
information in our [documentation on Selecting Tests](/documentation/selecting-tests).

Please [let us know](/contact) if you like the new features. We'd love to hear from you!

_Splash image courtesy of [Heeren Darji, Unsplash](https://unsplash.com/@heerenaway?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
