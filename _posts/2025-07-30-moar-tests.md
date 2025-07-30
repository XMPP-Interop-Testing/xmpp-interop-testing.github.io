---
layout: post
title:  "MOAR TESTS!"
cover-img: /assets/img/imagine-buddy_splash.jpg
date:   2025-07-30 22:24:12 +0200
author: Guus der Kinderen
categories:
---

Ever heard of XMPP Interop Testing? It's this cool project that helps make sure different XMPP servers can all work 
together smoothly. Our XMPP Interop Testing project provides a suite of automated tests that can be integrated into 
CI/CD pipelines to verify the compliance and interoperability of XMPP server implementations.

Late last year, we reported that we had secured funding graciously provided by [NLnet](https://nlnet.nl/) that allowed 
us to massively build out this project. This blog has been a bit quiet since then, but work has progressed. Significantly.

We have just released version 1.6.0 of all our test runners. With this release, we (again) more than doubled the total
number of XMPP interop tests! By my count, our project now lets loose 933 tests on your XMPP server implementation!

The biggest chunk of work has gone into tests that verify parts of the basic XMPP protocol, notably for testing
functionality that involves roster management (as specified in [section 2 of RFC 6121](https://datatracker.ietf.org/doc/html/rfc6121#autoid-7))
and for server rules for processing XML stanzas ([section 8 of RFC 6121](https://datatracker.ietf.org/doc/html/rfc6121#autoid-103)).

Additionally, a couple of new specifications are now being tested by our framework! Tests have been added for:

- [XEP-0133: Service Administration](https://xmpp.org/extensions/xep-0133.html)
- [XEP-0410: MUC Self-Ping (Schrödinger's Chat)](https://xmpp.org/extensions/xep-0410.html)
- [XEP-0421: Occupant identifiers for semi-anonymous MUCs](https://xmpp.org/extensions/xep-0421.html)
- [XEP-0433: Extended Channel Search](https://xmpp.org/extensions/xep-0433.html)

This table gives a complete comparison of test coverage between versions 1.5.0 and 1.6.0.

| Specification                                             | v1.5.0 | v1.6.0 | Difference |
|:----------------------------------------------------------| :--: | :--: | :--: |
| unknown                                                   | 13 | 13 | 0 |
| [RFC 6120](https://datatracker.ietf.org/doc/html/rfc6120) | 1 | 1 | 0 |
| [RFC 6121](https://datatracker.ietf.org/doc/html/rfc6121) | 11 | 402 | 391 |
| [XEP-0030](https://xmpp.org/extensions/xep-0030.html)     | 19 | 19 | 0 |
| [XEP-0045](https://xmpp.org/extensions/xep-0045.html)     | 252 | 252 | 0 |
| [XEP-0048](https://xmpp.org/extensions/xep-0048.html)     | 1 | 1 | 0 |
| [XEP-0050](https://xmpp.org/extensions/xep-0050.html)     | 4 | 4 | 0 |
| [XEP-0054](https://xmpp.org/extensions/xep-0054.html)     | 10 | 10 | 0 |
| [XEP-0060](https://xmpp.org/extensions/xep-0060.html)     | 24 | 24 | 0 |
| [XEP-0080](https://xmpp.org/extensions/xep-0080.html)     | 2 | 2 | 0 |
| [XEP-0085](https://xmpp.org/extensions/xep-0085.html)     | 1 | 1 | 0 |
| [XEP-0092](https://xmpp.org/extensions/xep-0092.html)     | 1 | 1 | 0 |
| [XEP-0096](https://xmpp.org/extensions/xep-0096.html)     | 2 | 2 | 0 |
| [XEP-0107](https://xmpp.org/extensions/xep-0107.html)     | 2 | 2 | 0 |
| [XEP-0115](https://xmpp.org/extensions/xep-0115.html)     | 12 | 12 | 0 |
| [XEP-0118](https://xmpp.org/extensions/xep-0118.html)     | 2 | 2 | 0 |
| [XEP-0133](https://xmpp.org/extensions/xep-0133.html)     | 0 | 44 | 44 |
| [XEP-0198](https://xmpp.org/extensions/xep-0198.html)     | 10 | 10 | 0 |
| [XEP-0199](https://xmpp.org/extensions/xep-0199.html)     | 2 | 2 | 0 |
| [XEP-0215](https://xmpp.org/extensions/xep-0215.html)     | 6 | 6 | 0 |
| [XEP-0232](https://xmpp.org/extensions/xep-0232.html)     | 1 | 1 | 0 |
| [XEP-0313](https://xmpp.org/extensions/xep-0313.html)     | 2 | 2 | 0 |
| [XEP-0347](https://xmpp.org/extensions/xep-0347.html)     | 3 | 3 | 0 |
| [XEP-0352](https://xmpp.org/extensions/xep-0352.html)     | 6 | 6 | 0 |
| [XEP-0363](https://xmpp.org/extensions/xep-0363.html)     | 12 | 12 | 0 |
| [XEP-0374](https://xmpp.org/extensions/xep-0374.html)     | 2 | 2 | 0 |
| [XEP-0384](https://xmpp.org/extensions/xep-0384.html)     | 4 | 4 | 0 |
| [XEP-0410](https://xmpp.org/extensions/xep-0410.html)     | 0 | 3 | 3 |
| [XEP-0421](https://xmpp.org/extensions/xep-0421.html)     | 0 | 67 | 67 |
| [XEP-0433](https://xmpp.org/extensions/xep-0433.html)     | 0 | 19 | 19 |
| [XEP-0486](https://xmpp.org/extensions/xep-0486.html)     | 4 | 4 | 0 |

To be clear: the work doesn't end here. There is still significant improvement to be made (and we've not yet used up
all of the grant either!) - we just liked to give you all an update. In the works are additional test implementations,
and a couple of new test runners. That should both increase coverage, but also allow our tests to be executed on even
more CI/CD platforms!

Please [get in touch](/contact) if you have any ideas for improvement, or other feedback. We'd love to hear from you!

- _Splash image courtesy of [Imagine Buddy, Unsplash](https://unsplash.com/photos/a-person-placing-a-piece-of-wood-into-a-pyramid-vsLbaIdhwaU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
