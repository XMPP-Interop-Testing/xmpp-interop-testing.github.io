---
layout: post
title: "Two New Features for Clearer Testing"
cover-img: /assets/img/provisioning-accounts_splash.jpg
date: 2025-09-26 20:39:32 +0100
author: Guus der Kinderen
categories:
---

We've just released version 1.7.1 of all of our test runners. This release adds two improvements to make interop testing
both stricter and easier to set up!

1. Impossible Tests Can Fail Runs

Some tests can't be executed if the server lacks required features. Previously, these "impossible" tests were skipped,
which could make a run look fully successful when it wasn't. Now you can configure the suite to treat impossible tests
as failures, ensuring that a green run really means every configured test executed and passed.

2. Flexible Account Provisioning

Our tests act like clients, so they need accounts to log in. You can now choose from three provisioning methods:

- Admin Account using [XEP-0133](https://xmpp.org/extensions/xep-0133.html) to create test accounts.
- Explicit Test Accounts you configure up front.
- In-Band Registration via [XEP-0077](https://xmpp.org/extensions/xep-0077.html).

Pick the approach that fits your setup best. There is [documentation](/documentation/provisioning-accounts) available 
for you to review the finer details!

Together, these features give you more reliable results and more flexibility in how you run tests!

_Splash image courtesy of [Mohamed Nohassi, Unsplash](https://unsplash.com/photos/a-group-of-white-robots-sitting-on-top-of-laptops-2iUrK025cec?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
