---
layout: post
title:  "First Prototype of Test Runner"
cover-img: /assets/img/cheer_splash.jpg
date:   2024-03-08 20:14:12 +0100
author: Guus der Kinderen
categories: 
---

> **It is alive, ALIVE!**

I'm so excited to have finished the first prototype of the first test runner that we are going to create!

As Dan & me are part of a team that is maintaining an XMPP server on GitHub, creating a GitHub Action that can be used
to super easily run the integration tests in a GitHub pipeline/flow was the obvious first prototype to tackle.

Things worked out beautifully!

## A wrapper for SINT
First, we've used a bit of prior art to create a new project that uses IgniteRealtime's [Smack Integration Test Framework](https://download.igniterealtime.org/smack/dailybuilds/sinttest-javadoc/org/igniterealtime/smack/inttest/package-summary.html),
and adds its own test implementations. Smack's tests are, after all, mostly client-oriented, while we're mostly interested
in having server-oriented tests. This new project was dubbed the [smack-sint-server-extensions](https://github.com/XMPP-Interop-Testing/smack-sint-server-extensions).

## A wrapper for the wrapper
Next, the `smack-sint-server-extension` artifact was easily embedded in a new GitHub Action: the [xmpp-interop-tests-action](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-action).

It is expected that this action is used in a continuous integration flow that creates a new build of the XMPP server
that is to be the subject of the tests.

Very generically, the xmpp-interop-test-action is expected to be part of such a flow in this manner:
1. Compile and build server software
2. Start server
3. **Invoke xmpp-interop-test-action**
4. Stop server

This could look something like the flow below:
{% raw  %}
```yaml
- name: Download Server distribution artifact from build job.
  uses: actions/download-artifact@v4
  with:
    name: my-server-distribution
    path: .

- name: Start CI server from distribution
  id: startCIServer
  uses: ./.github/actions/startserver-action # Should result in a running server.

- name: Run XMPP Interoperability Tests against CI server.
  uses: XMPP-Interop-Testing/xmpp-interop-tests-action@v1.0
  with:
    domain: 'shakespeare.lit'
    adminAccountUsername: 'juliet'
    adminAccountPassword: 'O_Romeo_Romeo!'

- name: Stop CI server
  if: ${{ always() && steps.startCIServer.conclusion == 'success' }}
  uses: ./.github/actions/stopserver-action
```
{% endraw  %}

Of course, we've immediately modified the continuous integration flow of our own XMPP server to make use of
`xmpp-interop-tests-action`. The proof of the pudding is in the tasting, after all! It worked!

Also, I'm happy to report that our server implementation passes all the tests that we're running. 😅

There's obviously still a lot of work to do, but, if you do feel adventurous and have a GitHub-based CI pipeline for an
XMPP project... have a go!

_Splash image courtesy of [Benjamin Davies, Unsplash](https://unsplash.com/photos/silhouette-of-man-standing-on-high-ground-under-red-and-blue-skies-FiZTaNTj2Ak?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
  