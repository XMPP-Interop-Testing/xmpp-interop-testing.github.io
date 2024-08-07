---
layout: page
title: XMPP Interop Testing
subtitle: XMPP standards compliance tests easily added to the test phase of your build pipeline
---

One of XMPP's largest challenges (and indeed of all open standards) is interoperability. We want to enable anyone to use the standards on any platform they like, written in the programming language they prefer, focussing design and utility toward any group, from enterprise to egg-cup enthusiasts.

When we came up with our idea for XMPP interop tests for server implementations, we knew we weren’t the first folk to ever have the idea. What was important for us wasn’t about getting the tests right, but about getting the tests used. With so many programming languages in play, we can’t get almost everyone to adopt an additional language into their toolchain. That’s a clear no-go.

Instead, we’re choosing to wrap our tests in the packaging of the common CI systems (a GitHub Action, a Circle CI Orb, etc) so that whatever your regular toolchain, you can slot the tests into the test phase of your build pipeline.

## Installation & Configuration

If you'd like to learn how to integrate XMPP standards conformance tests in your build pipeline, pick your continuous integration system below:

<div style="display:grid; grid-template-columns: 1fr 1fr 1fr 1fr; gap: 0.5em;">
{% include clickable_logo.html url="/documentation/github" img="/assets/img/GitHub-logo.png" description="GitHub" %}
{% include clickable_logo.html url="/documentation/gitlab" img="/assets/img/GitLab-logo.png" description="GitLab" %}
{% include clickable_logo.html url="/documentation/circleci" img="/assets/img/CircleCI-logo.png" description="Circle CI" %}
{% include clickable_logo.html url="/documentation/bamboo" img="/assets/img/Bamboo-logo.png" description="Bamboo" %}
{% include clickable_logo.html url="/documentation/docker" img="/assets/img/Docker-logo.png" description="Docker" %}
{% include clickable_logo.html url="/documentation/podman" img="/assets/img/Podman-logo.png" description="OCI, Podman, ContainerIO" %}
{% include clickable_logo.html url="/documentation/forgejo" img="/assets/img/Forgejo-logo.png" description="Forgejo, Codeberg" %}
</div>

## Usage

- [Choosing what test to run](/documentation/selecting-tests)
- [Diagnose test failures](/documentation/diagnose-test-failures)
