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
{% include clickable_logo.html url="/documentation/woodpecker" img="/assets/img/Woodpecker-logo.png" description="Woodpecker" %}
{% include clickable_logo.html url="/documentation/drone" img="/assets/img/Drone-logo.png" description="Drone" %}
{% include clickable_logo.html url="/documentation/forgejo" img="/assets/img/Forgejo-logo.png" description="Forgejo, Codeberg" %}
{% include clickable_logo.html url="/documentation/jenkins" img="/assets/img/Jenkins-logo.png" description="Jenkins" %}
</div>

## Usage

After integrating our tests in your build pipeline, you might be interested in the following usage documentation.

- [Choosing what test to run](/documentation/selecting-tests)
- [Diagnose test failures](/documentation/diagnose-test-failures)
