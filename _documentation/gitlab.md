---
layout: page
title:  "Testing in GitLab"
cover-img: /assets/img/infinite_splash.jpg
date:   2024-06-23 22:16:15 +0100
author: Dan Caseley
---

## Introduction

This guide is intended for XMPP Server developers who are building in GitLab. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. This assumes that you already have a pipeline established, and wish to add the additional checks.

Like all external components in GitLab, this one requires an include statement (with optional inputs). Since this job often requires your own dependencies and customisation, it imports as a template called `.xmpp-interop-tests` which is then explicit extended in a job. That job runs the tests, giving junit-esque outputs and debug logs. The only prerequisite is that you've got a built server, and have started it.

Assuming you already have a pipeline, and a script to launch your server, adding our GitLab component is as easy as adding a small amount YAML to your pipeline:

{% highlight yaml %}

  include:
    - component: gitlab.com/xmpp-interop-testing/xmpp-interop-testing-gitlab-component/interop-testing@main
      inputs:
        domain: 'shakespeare.lit'
        adminAccountUsername: 'juliet'
        adminAccountPassword: 'O_Romeo_Romeo!'

  compliance-tests:
    extends: .xmpp-interop-tests
    stage: test

{% endhighlight %}

The above assumes that when your server runs is running and reachable on the domain `shakespeare.lit` and that it is provisioned with an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. You'll find the full range of available configuration options below.

## A Full Example

Very generically, the job is expected to be part of such a flow in this manner:

1. Compile and build server software
2. Start server and run test job

Given the isolation of jobs in GitLab, the start of the server and the running of tests must normally be combined, usually by starting the server in the `before_script`.

Assume that you have a trivial pre-existing build pipeline that looks like this for compiling and storing builds of an XMPP server:

{% highlight yaml %}

  maven-build:
    stage: build
    script: ./mvnw package
    artifacts:
      paths:
        - my_xmpp_server

{% endhighlight %}  

Then you'd add our first example, with tweaks for dependencies and for launching, and end up with this:

{% highlight yaml %}

  maven-build:
    stage: build
    script: ./mvnw package
    artifacts:
      paths:
        - my_xmpp_server

  include:
    - component: gitlab.com/xmpp-interop-testing/xmpp-interop-testing-gitlab-component/interop-testing@main
      inputs:
        domain: 'shakespeare.lit'
        host: '127.0.0.1'
        adminAccountUsername: 'juliet'
        adminAccountPassword: 'O_Romeo_Romeo!'
  
  compliance-tests:
    extends: .xmpp-interop-tests
    stage: test
    dependencies:
      - maven-build
    before_script: "./my_xmpp_server/launch-my-server.sh"

{% endhighlight %}

In addition to the first example:

- A `host` option to direct the test engine to resolve `shakespeare.lit` as the local server
- A `dependencies` section in the test job to wait for the build
- A `before_script` to launch the built server prior to testing

## Configuration

Various options are available when calling _xmpp_interop_tests_action_, and whilst none of them are absolutely required, the defaults are unlikely to be perfect for everyone.

| Option                 | Description                                                                                                                                                                                                                                                                           | Default value      |
|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| ------------------ |
| host                   | IP address or DNS name of the XMPP service to run the tests on.                                                                                                                                                                                                                       | 127.0.0.1          |
| domain                 | the XMPP domain name of server under test.                                                                                                                                                                                                                                            | example.org        |
| timeout                | the amount of milliseconds after which an XMPP action (typically an IQ request) is considered timed out.                                                                                                                                                                              | 60000 (one minute) |
| adminAccountUsername   | (optional) The account name of a pre-existing user that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html). If not provided, in-band registration ([XEP-0077](https://xmpp.org/extensions/xep-0077.html)) will be used to provision accounts | -                  |
| adminAccountPassword   | (optional) The password of the admin account                                                                                                                                                                                                                                          | -                  |
| disabledTests          | (optional) A comma-separated list of tests that are to be skipped. For example: EntityCapsTest,SoftwareInfoIntegrationTest                                                                                                                                                            | -                  |
| disabledSpecifications | (optional) A comma-separated list of specifications (not case-sensitive) that are to be skipped. For example: XEP-0045,XEP-0060                                                                                                                                                       | -                  |
| logDir                 | (optional) The directory in which the test output and logs are to be stored. This directory will be created, if it does not already exist.                                                                                                                                            | ./output           |

For the latest updates to the documentation of the configuration updates, consult the [GitLab repository](https://gitlab.com/xmpp-interop-testing/xmpp-interop-testing-gitlab-component).

{% include doc-next-steps.html %}

_Splash image courtesy of [Joel Filipe, Unsplash](https://unsplash.com/photos/low-angle-photo-of-30-st-mary-axe-VuwAfoHpxgs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
