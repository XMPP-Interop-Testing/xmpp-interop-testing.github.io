---
layout: page
title: XMPP Interop Testing
subtitle: XMPP standards compliance tests easily added to the test phase of your build pipeline
---

One of XMPP's largest challenges (and indeed of all open standards) is interoperability. We want to enable anyone to use the standards on any platform they like, written in the programming language they prefer, focussing design and utility toward any group, from enterprise to egg-cup enthusiasts.

When we came up with our idea for XMPP interop tests for server implementations, we knew we weren’t the first folk to ever have the idea. What was important for us wasn’t about getting the tests right, but about getting the tests used. With so many programming languages in play, we can’t get almost everyone to adopt an additional language into their toolchain. That’s a clear no-go.

Instead, we’re choosing to wrap our tests in the packaging of the common CI systems (a GitHub Action, a Circle CI Orb, etc) so that whatever your regular toolchain, you can slot the tests into the test phase of your build pipeline.

## Who's this for?

The intended target audience of this project consists of XMPP server software _developers_. The tests that are provided by this project aim to identify problems in the _implementation_ of XMPP server software.  We provide tooling that is explicitly designed to be used in the continuous integrations systems that are typically used in server software development.

Our project is _not_ designed verify the _configuration_ of an XMPP server. Although there is overlap, these are two different beasts. For people that _administer_ an XMPP server, this project is probably less suitable. They likely benefit more from tooling like [the XMPP Compliance Tester](https://compliance.conversations.im/).

## Installation & Configuration

If you'd like to learn how to integrate XMPP standards conformance tests in your build pipeline, pick your continuous integration system below:

<div style="display:grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 0.5em;">
{% include clickable_logo.html url="/documentation/github" img="/assets/img/GitHub-logo.png" description="GitHub" %}
{% include clickable_logo.html url="/documentation/gitlab" img="/assets/img/GitLab-logo.png" description="GitLab" %}
{% include clickable_logo.html url="/documentation/circleci" img="/assets/img/CircleCI-logo.png" description="Circle CI" %}
{% include clickable_logo.html url="/documentation/bamboo" img="/assets/img/Bamboo-logo.png" description="Bamboo" %}
{% include clickable_logo.html url="/documentation/docker" img="/assets/img/Docker-logo.png" description="Docker" %}
{% include clickable_logo.html url="/documentation/podman" img="/assets/img/Podman-logo.png" description="OCI, Podman, ContainerIO" %}
{% include clickable_logo.html url="/documentation/harness" img="/assets/img/Harness-logo.png" description="Harness" %}
{% include clickable_logo.html url="/documentation/woodpecker" img="/assets/img/Woodpecker-logo.png" description="Woodpecker" %}
{% include clickable_logo.html url="/documentation/drone" img="/assets/img/Drone-logo.png" description="Drone" %}
{% include clickable_logo.html url="/documentation/forgejo" img="/assets/img/Forgejo-logo.png" description="Forgejo, Codeberg" %}
{% include clickable_logo.html url="/documentation/jenkins" img="/assets/img/Jenkins-logo.png" description="Jenkins" %}
</div>

## Usage

After integrating our tests in your build pipeline, you might be interested in the following usage documentation.

- [Choosing what test to run](/documentation/selecting-tests)
- [Diagnose test failures](/documentation/diagnose-test-failures)

## What is being tested?

The tests mimic clients that interact with the server under test. All tests are client-to-server based tests. Server-to-server tests aren't currently included, but those might be added in the future.

The tests verify behavior as specified by the many RFCs and XEPs that govern the XMPP protocol. At the time of writing, tests exist for these specifications:

- [RFC 6120: Extensible Messaging and Presence Protocol (XMPP): Core](https://datatracker.ietf.org/doc/html/rfc6120)
- [RFC 6121: Extensible Messaging and Presence Protocol (XMPP): Instant Messaging and Presence](https://datatracker.ietf.org/doc/html/rfc6121)
- [XEP-0030: Service Discovery](https://xmpp.org/extensions/xep-0030.html)
- [XEP-0045: Multi-User Chat](https://xmpp.org/extensions/xep-0045.html)
- [XEP-0048: Bookmarks](https://xmpp.org/extensions/xep-0048.html)
- [XEP-0050: Ad-Hoc Commands](https://xmpp.org/extensions/xep-0050.html)
- [XEP-0054: vcard-temp](https://xmpp.org/extensions/xep-0054.html)
- [XEP-0060: Publish-Subscribe](https://xmpp.org/extensions/xep-0060.html)
- [XEP-0080: User Location](https://xmpp.org/extensions/xep-0080.html)
- [XEP-0085: Chat State Notifications](https://xmpp.org/extensions/xep-0085.html)
- [XEP-0092: Software Version](https://xmpp.org/extensions/xep-0092.html)
- [XEP-0096: SI File Transfer](https://xmpp.org/extensions/xep-0096.html)
- [XEP-0107: User Mood](https://xmpp.org/extensions/xep-0107.html)
- [XEP-0115: Entity Capabilities](https://xmpp.org/extensions/xep-0115.html)
- [XEP-0118: User Tune](https://xmpp.org/extensions/xep-0118.html)
- [XEP-0133: Service Administration](https://xmpp.org/extensions/xep-0133.html)
- [XEP-0198: Stream Management](https://xmpp.org/extensions/xep-0198.html)
- [XEP-0199: XMPP Ping](https://xmpp.org/extensions/xep-0199.html)
- [XEP-0215: External Service Discovery](https://xmpp.org/extensions/xep-0215.html)
- [XEP-0232: Software Information](https://xmpp.org/extensions/xep-0232.html)
- [XEP-0313: Message Archive Management](https://xmpp.org/extensions/xep-0313.html)
- [XEP-0347: Internet of Things - Discovery](https://xmpp.org/extensions/xep-0347.html)
- [XEP-0352: Client State Indication](https://xmpp.org/extensions/xep-0352.html)
- [XEP-0363: HTTP File Upload](https://xmpp.org/extensions/xep-0363.html)
- [XEP-0374: OpenPGP for XMPP Instant Messaging](https://xmpp.org/extensions/xep-0374.html)
- [XEP-0384: OMEMO Encryption](https://xmpp.org/extensions/xep-0384.html)
- [XEP-0410: MUC Self-Ping (Schrödinger's Chat)](https://xmpp.org/extensions/xep-0410.html)
- [XEP-0421: Occupant identifiers for semi-anonymous MUCs](https://xmpp.org/extensions/xep-0421.html)
- [XEP-0433: Extended Channel Search](https://xmpp.org/extensions/xep-0433.html)
- [XEP-0486: MUC Avatars](https://xmpp.org/extensions/xep-0486.html)

The set of tested specifications changes frequently (and we might not be the best at keeping the list on this page completely up-to-date).

Our tests do not necessarily cover the listed specifications completely, for a couple of reasons:
- We've simply not been able to finish all tests. The amount of XMPP-related specifications numbers in the hundreds. On top of that: some specifications are lengthy, _lengthy_ documents, for which more effort is needed to complete test coverage. Feel free to lend us a hand, either by implementing tests yourself, or making available resources for us to do so! If you're considering this, [please reach out to us](/contact).
- Secondly, we have opted to cover only the bits of the specification that are defined to be mandatory ('REQUIRED', 'MUST' and 'SHALL' in [RFC 2119-speak](https://datatracker.ietf.org/doc/html/rfc2119)). Other requirement indicators ('SHOULD', 'MAY') signal wiggle-room: for those parts of a specification, a server _could_ possibly act differently than what is specified, which _can_ be acceptable in certain contexts. We prefer to have test failures to unequivocally indicate a failure (which is hard enough in itself)! We might add tests for other requirement indicators in the future (guarded by some kind of configuration option), but for now, there are plenty of tests that we can create that do not suffer from this ambiguity. We've opted to start with those.

The order in which we're creating tests is loosely based on the [XMPP Compliance Suites](https://xmpp.org/about/compliance-suites/), that provide guidance on which specifications are important when implementing an application of a certain kind. We've also added tests that happen to be already available, or that were created in an effort to tackle some kind of development issue that one of us was working on in the past.
