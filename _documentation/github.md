---
layout: page
title:  "Testing in Github"
cover-img: /assets/img/github_splash.jpg
date:   2024-02-18 19:45:53 +0000
author: Dan Caseley
---

_This page is a work in progress. Please bear with us whilst we get our documentation together._

Go places. Click things. End up with something like this:

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
  
_Splash image courtesy of [Roman Synkevych, Unsplash](https://unsplash.com/photos/black-and-white-penguin-toy-wX2L8L-fGeA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
