---
layout: page
title:  "Testing in CircleCI"
cover-img: /assets/img/circle-ci_splash.jpg
date:   2024-05-16 16:38:09 +0200
author: Guus der Kinderen
---

## Introduction

This guide is intended for XMPP Server developers who are building their server in a Circle CI. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. This assumes that you already have a pipeline established, and wish to add the additional checks.

The checks run as their own action within the pipeline, in which they'll execute tests and output junit-esque and debugging output. The only prerequisite is that you've got a built server and have started it.

Assuming that you have a pre-existing pipeline that build your server and starts it for integration testing, then adding our Circle CI Orb is as easy as adding one step to your pipeline, like this:

{% highlight yaml %}

- xmpp-interop-tests/test:
    domain: shakespeare.lit
    timeout: 60000
    adminAccountUsername: juliet
    adminAccountPassword: O_Romeo_Romeo!

{% endhighlight %}


The above assumes that your server is running and reachable on the domain `shakespeare.lit` and that it is provisioned with an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. You'll find the full range of available configuration options below.

## A Full Example

Assume that you have a pre-existing build pipeline that looks like this for building your XMPP server:

{% highlight yaml %}

description: >
  Run an interop tests step as part of an XMPP server build process

usage:
  version: 2.1

  jobs:
    build:
      docker:
        - image: cimg/base:2024.02
      steps:
        - run: echo "Build your server here"
        - persist_to_workspace:
            paths:
              - ./my-server

  workflows:
    build-my-server:
      jobs:
        - build

{% endhighlight %}


Here, we are hand-waving around the actual check out of the code and setting up of tooling. We do make available the server artifact as a path, to be used further down the line.

A second job that may already exist uses the build output, and launches a server to be used for integration testing.

{% highlight yaml %}

    test:
      docker:
        - image: cimg/openjdk:17.0
      steps:
        - run:
            command: ./my-server/run.sh
            background: true
        - run:
            command: ./my-server/execute-my-tests.sh

{% endhighlight %}

The workflow is adjusted to run the additional `test` step after the `build` step completes:

{% highlight yaml %}

  workflows:
    build-my-server:
      jobs:
        - build
        - test:
            requires:
              - build


{% endhighlight %}

We've added a second job to the end of the first which fetches a launch script and the freshly-built binary, runs the launch script to run the server in the background, then calls some script to run whatever tests that you already had in your project.

The above combines into a pipeline that you may already have in your project, or something that you would add in preparation of running our Interoperability Tests. It is all highly specific to your project.

To run our Interoperability Tests, only one step needs to be added to this. This step makes use of our Orb, that you'll need to define in the top of your configuration:

{% highlight yaml %}

  orbs:
    xmpp-interop-tests: xmpp-interop-tests/tests@1.0.0

{% endhighlight %}

With the Orb defined, a step that uses it can be added to your pre-existing job:

{% highlight yaml %}

      - xmpp-interop-tests/test:
          domain: shakespeare.lit
          timeout: 60000
          adminAccountUsername: juliet
          adminAccountPassword: O_Romeo_Romeo!

{% endhighlight %}

This step will use our Circle CI Orb to run the XMPP Interoperability Tests against your server. Look below for additional configuration options.

For completeness, here is the full pipeline, combining everything above:

{% highlight yaml %}

description: >
  Run an interop tests step as part of an XMPP server build process

usage:
  version: 2.1

  orbs:
    xmpp-interop-tests: xmpp-interop-tests/tests@1.0.0

  jobs:
    build:
      docker:
        - image: cimg/base:2024.02
      steps:
        - run: echo "Build your server here"
        - persist_to_workspace:
            paths:
              - ./my-server
    test:
      docker:
        - image: cimg/openjdk:17.0
      steps:
        - run:
            command: ./my-server/run.sh
            background: true
        - run:
            command: ./my-server/execute-my-tests.sh
        - xmpp-interop-tests/test:
            domain: shakespeare.lit
            timeout: 60000
            adminAccountUsername: juliet
            adminAccountPassword: O_Romeo_Romeo!

  workflows:
    build-my-server:
      jobs:
        - build
        - test:
            requires:
              - build

{% endhighlight %}

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

For the latest updates to the documentation of the configuration updates, consult the [GitHub repository](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-circleci-orb).

Note that we have a dedicated bit of documentation that describes [choosing what tests to run](selecting-tests).

_Splash image courtesy of [Circle CI Blogs](https://circleci.com/blog/what-is-a-ci-cd-pipeline/)_
