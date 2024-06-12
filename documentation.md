---
layout: page
title: Documentation
subtitle: How to test a server implementation for standards compliance using this project
---

_This page is a work in progress. Please bear with us whilst we get our documentation together._

## Installation & Configuration

If you'd like to learn how to integrate XMPP standards conformance tests on your site, pick your CI system below:

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

- [Diagnose test failures](/documentation/diagnose-test-failures)
- [Choosing what test to run](/documentation/selecting-tests)