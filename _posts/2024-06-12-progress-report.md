---
layout: post
title:  "Progress Report"
cover-img: /assets/img/progressreport_splash.jpg
date:   2024-06-12 15:35:07 +0200
author: Guus der Kinderen
categories:
---

We've been quiet for a while, but we've been busy! Over the last few months, things have progressed nicely. Let me catch you up!

## New Test Runners

In my [last blogpost](/2024/03/08/first-test-abstraction-prototype.html), I wrote about the first Test Runner that we created: one that is usable as a [GitHub Action](/documentation/github).

We are continuing to improve that runner, but I'm happy to report that we now also have runners for:
- [GitLab](/documentation/gitlab)
- [Circle CI](/documentation/circleci)
- [Atlassian Bamboo](/documentation/bamboo)

(Please bear with us while we get the documentation in order).

What we've not yet crossed off the to-do list includes runners for:
- [Docker](/documentation/docker)
- [OCI, Podman & ContainerIO](/documentation/podman)
- [Forgejo & Codeberg](/documentation/forgejo)

... and maybe Jenkins? 😬 I'm still on the fence, and I keep forgetting to poke Dan about that one. **Hey Dan!** Whaddayathink?

## Smack improvements

The testing framework that we're using for most of the tests is provided by IgniteRealtime's [Smack Integration Test Framework](https://download.igniterealtime.org/smack/dailybuilds/sinttest-javadoc/org/igniterealtime/smack/inttest/package-summary.html). Through continuous usage of that framework, we ran into several bits and bobs that we wanted that Framework to have, do or do differently. With help of Smack's lead developer, Florian, a couple of nice additions have been added.

The rationale for many of these improvements is the same: We recognized that it is incredibly difficult to figure out _why_ a particular test fails, especially since we do not expect people that run our tests to be familiar with Smack, the SINT framework, our test implementations, or even the programming language that is used to create them. We have put in effort to improve this.

We've set out to make this easier, documenting [a comprehensive how-to guide](/documentation/diagnose-test-failures) in the process.

#### Annotations

SINT tests can now be annotated to reference specifications. This allows us to tell which implementation is testing which specification. That in turn allows us to tell you not only which test fails, but also to which specification/functionality that test relates. We can even quote the exact section of the specification for which a test is failing now.

#### Debug Logging

Although debug logging (the act of logging XMPP stanzas related to any particular test) was already configurable in SINT, we've made that more flexible. This allowed us to write a custom logger, that generates debug files per test.

Having XMPP traffic dumps _per test_ makes it much easier to debug the reason for a test failure. When a test fails, you can now easily see the exact XMPP traffic that was exchanged with the server under test.

Additionally, we've whipped up a test result processor that generates XML files in the JUnit format of test result reporting. Our thought here is that many build systems have native support for these files, which may lead to quick wins with regard to test result presentation in these systems. The proof of the pudding is in the eating though, so lets see how this pans out.

#### Assertion messages

We've put in quite some effort to improve the reporting of assertion failures. We have tried to make sure that an assertion failure will report a human-readable message that can be understood without looking at the source code of the test.

This, combined with the other improvements described above, should allow for an XMPP developer to deduce the reason for most test failures.

## Improved test coverage!

We've also been busy writing new tests! So far, we've improved coverage for:
- [XEP-0030: Service Discover](https://xmpp.org/extensions/xep-0030.html)
- [XEP-0045: Multi-User Chat](https://xmpp.org/extensions/xep-0045.html)
- [XEP-0352: Client State Indication](https://xmpp.org/extensions/xep-0352.html)

If you're wondering why these: it is not completely arbitrary: we're prioritizing the specifications mentioned in the [XMPP Compliance Suites](https://xmpp.org/about/compliance-suites/), but combine that with the available APIs in the libraries that we use. Obviously, we're planning to add more and more coverage over time.

## Next steps

That about sums up what we've been busy with. Over the next few weeks, we'll continue to build new runners and tests.

I'm also contemplating asking one or two server developers, that are unfamiliar with this project, to have a go and try to integrate one of our runners in their server project's CI pipeline. I'm hoping that this results in feedback that allows us to improve the usability of the project. I'm not quite sure about the timing of this though. I'll think it over...

Until next time!

- _Splash image courtesy of [Isaac Smith, Unsplash](https://unsplash.com/@isaacmsmith?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
  