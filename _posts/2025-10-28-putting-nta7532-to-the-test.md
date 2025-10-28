---
layout: post
title: Putting NTA 7532 to the Test (Literally)
cover-img: /assets/img/adaptor_splash.jpg
date: 2025-10-28 19:40:06
author: Dan Caseley
categories:
---

You might have seen the [XMPP Standards Foundation's open letter](https://xmpp.org/2025/10/towards-secure-and-interoperable-healthcare-chat/) to NEN about **NTA 7532**, the Dutch effort to standardise secure healthcare chat. It's a good read, and, as it happens, right up our street.

If you're building a chat system that has to *actually talk* to someone else's chat system (and keep doctors happy while doing it), you'll know: writing a specification is only half the battle. The other half is making sure that everyone follow it, and that everyone follows it in the same way.

That's where the **XMPP Interop Testing Framework** comes in.

## So, What Do We Do Again?

In short: we make sure XMPP software behaves the way the standards say it should.

We've built an open-source test framework that runs a bunch of automated checks against real XMPP servers using a real XMPP client library, testing everything from the core RFCs (6120, 6121, 7622) to the popular protocol extensions for things like:

- message receipts (`XEP-0184`)
- group chat (`XEP-0045`)
- file upload (`XEP-0363`)
- end-to-end encryption

It's all designed to run in CI, with containers, and produce nice, clear pass/fail results, along with machine-consumable reports and human-readbale actionable information. The kind you can wave around in a meeting and say "See? Interoperable!"

## Why NTA 7532 Folks Should Care

NTA 7532 is about making sure healthcare professionals can message each other securely, even when they're on different systems and members of different organizations. That means encryption, integrity, and *actual interoperability* between products from different vendors.

You could write those requirements into a 200-page document (and you probably will). But to prove it works, you need tests. Preferably ones that don't take a week to run by hand, and that aren't only run just prior to launch and never again.

That's exactly what we provide. 

Our framework already checks for the building blocks that NTA 7532 is likely to depend on: authentication, transport security, message delivery, receipts, and so on.  And because the tests are open and automated, every vendor can run the same suite - no secret sauce or proprietary knowledge required.

## From "We Think" to "We Know"

Here's the value add:

1. **Validation** - The framework tells you, with logs and evidence, whether a given implementation matches the spec or standard. 
2. **Transparency** - Everyone can see what's tested and why and how. The same tests for everyone, with the same criteria.
3. **Continuous improvement** - When specs change or new features appear, we add new tests. Easy.

It turns a written standard into a *living, testable thing*. If you want to know whether two systems will work together before putting them in front of clinicians, this is how you find out.

## The Bigger Picture

The fun part is collaboration. 

The XSF writes and maintains the XMPP specs. 
NEN and the folks behind NTA 7532 define the national healthcare chat profile. 
And we, the **Interop Testing Framework** team, provide the bit in the middle: the place where specs meet running code.

Together, we can prove that "open standard" isn't just a phrase, but that it's something you can test, verify, and rely upon.

## What's Next

We'd love to:

- run **pilot tests** with any NTA 7532-aligned vendors
- map specific NTA 7532 requirements to existing (or new) XMPP test cases 
- publish anonymised results to show real-world interoperability 
- feed our findings back to both the NTA 7532 working group and to the XSF

If that sounds like something you'd like to be part of: fantastic!

Come talk to us.

## Get Involved

The framework's open-source, so dive right in:

- [Website](https://xmpp-interop-testing.github.io) 
- [Code](https://github.com/xmpp-interop-testing)

Whether you're writing specs, building servers, or just trying to get two chat systems to agree on a message receipt, we're here for you.

Let's make interoperability not just a checkbox, but a test you can actually pass ✅

_Splash image courtesy of [Marcus Urbenz, Unsplash](https://unsplash.com/photos/black-and-silver-microphone-on-white-computer-keyboard-qY6ikaS8L38?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_
