---
layout: page
title:  "Test Account Provisioning"
cover-img: /assets/img/provisioning-accounts_splash.jpg
date:   2025-09-15 14:12:30 +0200
author: Guus der Kinderen
---

The tests that make up the test suite mimic clients that interact with the server under test: Tests are client-to-server
based tests. As such, most tests require a username and password to be able to connect to the server. This guide describes
three alternative methods for the tests to use accounts:

- **Admin Account** - By configuring the username and password of a pre-existing administrative user, test accounts will be created using [XEP-0133: Service Administration](https://xmpp.org/extensions/xep-0133.html) functionality.
- **Explicit Test Accounts** - You can configure three pre-existing accounts that will be used for testing.
- **In-Band Registration** - In-band registration ([XEP-0077](https://xmpp.org/extensions/xep-0077.html)) can be used to provision accounts.

### Caveats
Some test implementations require a specific method of account provisioning (or are not compatible with one of the 
provisioning methods). Notably, some tests require administrative functionality, that is only available with the 
'admin account' method. Other tests require on-demand account creation, which isn't available with the 'explicit test
accounts' method.

When a test can't run because of the type of provisioning that's configured, that test is automatically skipped. This
affects only a small portion of the total test suite that's available within the framework.

For best results, the 'admin account' method is preferable over the other methods.

## Admin Account (and ad-hoc commands)

Many XMPP servers have the notion of 'administrative' functionality that can be invoked via a subset of the XMPP protocol,
named Ad-Hoc Commands. This functionality is standardized in [XEP-0133: Service Administration](https://xmpp.org/extensions/xep-0133.html).

By configuring the username and password of a pre-existing user that is permitted to invoke this functionality, the test
framework will create three test accounts on the server under test.

To configure the credentials for this user, the `adminAccountUsername` and `adminAccountPassword` configuration options
are to be used.

## Explicit Test Accounts

Most tests use up to three accounts. Whereas the other methods in this document allow the test framework to create these
accounts dynamically, the framework can also be configured to use predefined accounts instead. This allows you to
provision the accounts out of band, in any other way that is appropriate to your environment.

To configure credentials for all three test accounts, use the following configuration option pairs:

- `accountOneUsername` and `accountOnePassword` 
- `accountTwoUsername` and `accountTwoPassword`
- `accountThreeUsername` and `accountThreePassword`

Note that all six configuration options need to be provided, and they all need to reference different user accounts.

## In-Band Registration

When neither the "Admin Account" nor the "Explicit Test Accounts" options is configured, the test framework will fall
back to a method of last resort: using an XMPP feature called In-Band Registration (as specified in
[XEP-0077](https://xmpp.org/extensions/xep-0077.html)). Using this method, test accounts are created on demand.

In-Band Registration typically is not enabled by default on servers. If it is, it may require additional security checks
(such is CAPTCHA solving) that is not supported by our test framework.

_Splash image courtesy of [Mohamed Nohassi, Unsplash](https://unsplash.com/photos/a-group-of-white-robots-sitting-on-top-of-laptops-2iUrK025cec?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
