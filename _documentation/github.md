---
layout: page
title:  "Testing in Github"
cover-img: /assets/img/github_splash.jpg
date:   2024-02-18 19:45:53 +0000
author: Dan Caseley
---

## Introduction

This guide is intended for XMPP Server developers who are building on Github Actions. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. This assumes that you already have a pipeline established, and wish to add the additional checks.

The checks run as their own action within the pipeline, in which they'll execute tests and output junit-esque and debugging output. The only prerequisite is that you've got a built server and have started it.

Assuming that you have a pre-existing pipeline that build your server and starts it for integration testing, then adding our Github Action is as easy as adding one step to your pipeline, like this:

{% highlight yaml %}

- name: Run XMPP Interoperability Tests against CI server.
  uses: XMPP-Interop-Testing/xmpp-interop-tests-action@v1.4.0
  with:
    domain: 'shakespeare.lit'
    adminAccountUsername: 'juliet'
    adminAccountPassword: 'O_Romeo_Romeo!'

{% endhighlight %}


The above assumes that your server is running and reachable on the domain `shakespeare.lit` and that it is provisioned with an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. You'll find the full range of available configuration options below.

## A Full Example

Assume that you have a pre-existing build pipeline that looks like this for building an XMPP server called "eXaMPPle":

{% highlight yaml %}

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Java
        uses: actions/setup-java@v4
      - name: Build with Maven
        run: mvn package
      - name: Stash the built artifacts
        uses: actions/upload-artifact@v4
        with:
          name: eXaMPPle
          path: target/example.jar

{% endhighlight %}

Here, you check out the code, set up some tooling, then run a process that builds, unit tests and produces a binary output, which we then archive as an artifact.

A second job that may already exist uses the build output, and launches a server to be used for integration testing.

{% highlight yaml %}

  test:
    name: Compliance Tests
    needs: build # Sequential, not parallel to the build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout convenience scripts
        uses: actions/checkout@v4
        with:
          sparse-checkout: |
            ci-scripts

      - name: Download distribution artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: eXaMPPle
          path: .

      - name: Start server from build job
        id: startServer
        uses: ./ci-scripts/launch-server # Launch the eXaMPPle server with test configs

      - name: Run eXaMMple integration tests against CI server
        run: ./ci-scripts/execute-tests # Run tests that already existed in your pipeline

{% endhighlight %}


We've added a second job to the end of the first which fetches a launch script and the freshly-built binary, runs the launch script to run the _eXaMPPle_ server in the background, then calls some script to run whatever tests that you already had in your project.

The above combines into a pipeline that you may already have in your project, or something that you would add in preparation of running our Interoperability Tests. It is all highly specific to your project.

To run our Interoperability Tests, only one step needs to be added to this:

{% highlight yaml %}

      - name: Run XMPP Interoperability Tests against CI server.
        uses: XMPP-Interop-Testing/xmpp-interop-tests-action@v1.4.0
        with:
          domain: 'shakespeare.lit'
          adminAccountUsername: 'juliet'
          adminAccountPassword: 'O_Romeo_Romeo!'

{% endhighlight %}

This step will use our Github Action to run the XMPP Interoperability Tests against your server. Look below for additional configuration options.

For completeness, here is the full pipeline, combining everything above:

{% highlight yaml %}

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Java
        uses: actions/setup-java@v4
      - name: Build with Maven
        run: mvn package
      - name: Stash the built artifacts
        uses: actions/upload-artifact@v4
        with:
          name: eXaMPPle
          path: target/example.jar

  test:
    name: Compliance Tests
    needs: build # Sequential, not parallel to the build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout convenience scripts
        uses: actions/checkout@v4
        with:
          sparse-checkout: |
            ci-scripts

      - name: Download distribution artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: eXaMPPle
          path: .

      - name: Start server from build job
        id: startServer
        uses: ./ci-scripts/launch-server # Launch the eXaMPPle server with test configs

      - name: Run eXaMMple integration tests against CI server
        run: ./ci-scripts/execute-tests # Run tests that already existed in your pipeline

      - name: Run XMPP Interoperability Tests against CI server
        uses: XMPP-Interop-Testing/xmpp-interop-tests-action@v1.4.0
        with:
          domain: 'shakespeare.lit'
          adminAccountUsername: 'juliet'
          adminAccountPassword: 'O_Romeo_Romeo!'

{% endhighlight %}

## Configuration

Various options are available when calling _xmpp_interop_tests_action_, and whilst none of them are absolutely required, the defaults are unlikely to be perfect for everyone.

| Option                 | Description                                                                                                                                                                                                                                                                           | Default value       |
|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| host                   | IP address or DNS name of the XMPP service to run the tests on.                                                                                                                                                                                                                       | 127.0.0.1           |
| domain                 | the XMPP domain name of server under test.                                                                                                                                                                                                                                            | example.org         |
| timeout                | the amount of milliseconds after which an XMPP action (typically an IQ request) is considered timed out.                                                                                                                                                                              | 5000 (five seconds) |
| adminAccountUsername   | (optional) The account name of a pre-existing user that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html). If not provided, in-band registration ([XEP-0077](https://xmpp.org/extensions/xep-0077.html)) will be used to provision accounts | -                   |
| adminAccountPassword   | (optional) The password of the admin account                                                                                                                                                                                                                                          | -                   |
| disabledTests          | (optional) A comma-separated list of tests that are to be skipped. For example: EntityCapsTest,SoftwareInfoIntegrationTest                                                                                                                                                            | -                   |
| disabledSpecifications | (optional) A comma-separated list of specifications (not case-sensitive) that are to be skipped. For example: XEP-0045,XEP-0060                                                                                                                                                       | -                   |
| logDir                 | (optional) The directory in which the test output and logs are to be stored. This directory will be created, if it does not already exist.                                                                                                                                            | ./output            |

For the latest updates to the documentation of the configuration updates, consult the [GitHub repository](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-action).

{% include doc-next-steps.html %}

_Splash image courtesy of [Roman Synkevych, Unsplash](https://unsplash.com/photos/black-and-white-penguin-toy-wX2L8L-fGeA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
