---
layout: page
title:  "Testing in Github"
cover-img: /assets/img/github_splash.jpg
date:   2024-02-18 19:45:53 +0000
author: Dan Caseley
---

## Introduction

This guide is intended for XMPP Server developers who building on Github Actions. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. This assumes that you already have a pipeline established, and wish to add the additional checks.

The checks run as their own action within the pipeline, in which they'll execute tests and output junit-esque and debugging output. The only prerequisite is that you've got a built server and have started it.

## An Example

Assume a build pipeline that looks like this for building an XMPP Server called "eXaMPPle":

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


Here, we check out the code, set up some tooling, then run a process that builds, unit tests and produces a binary output, which we then archive as an artifact.

To add the compliance tests, we add a second job which uses the output of the first. Since we need to launch the server, and we're going to do that with a particular configuration (e.g. with some known ports and perhaps a known administrative user), I've assumed a sparse checkout of some convenience scripts that wrap this for our imaginary server.

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

      - name: Download distribution artifact from build job.
        uses: actions/download-artifact@v4
        with:
          name: eXaMPPle
          path: .

      - name: Start server from build job
        id: startServer
        uses: ./ci-scripts/launch-server # Launch the eXaMPPle server with test configs

      - name: Run XMPP Interoperability Tests against CI server.
        uses: XMPP-Interop-Testing/xmpp-interop-tests-action@v1.2
        with:
          domain: 'shakespeare.lit'
          adminAccountUsername: 'juliet'
          adminAccountPassword: 'O_Romeo_Romeo!'

{% endhighlight %}

We've added a second job to the end of the first which fetches a launch script and the freshly-built binary, runs the launch script to run the _eXaMPPle_ server in the background, then calls _xmpp-interop-tests-action_ to run the tests.

## Configuration

Various options are available when calling _xmpp_interop_tests_action_, and whilst none of them are absolutely required, the defaults are unlikely to be perfect for everyone.

| Option                 | Description                                                                                                                                                                                 | Default value      |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| ipAddress              | the IP address of the server under test                                                                                                                                                     | 127.0.0.1          |
| domain                 | the XMPP domain name of server under test                                                                                                                                                   | example.org        |
| timeout                | the amount of milliseconds after which an XMPP action (typically an IQ request) is considered timed out.                                                                                    | 60000 (one minute) |
| adminAccountUsername   | (optional) The account name of a pre-existing user that is allowed to create other users, per XEP-0133. If not provided, in-band registration (XEP-0077) will be used to provision accounts | -                  |
| adminAccountPassword   | (optional) The password of the admin account                                                                                                                                                | -                  |
| disabledTests          | (optional) A comma-separated list of tests that are to be skipped. For example: EntityCapsTest,SoftwareInfoIntegrationTest                                                                  | -                  |
| disabledSpecifications | (optional) A comma-separated list of specifications (not case-sensitive) that are to be skipped. For example: XEP-0045,XEP-0060                                                             | -                  |
| logDir                 | (optional) The directory in which the test output and logs are to be stored. This directory will be created, if it does not already exist.                                                  | ./output           |


For the latest documentation, consult the [GitHub repository](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-action)

_Splash image courtesy of [Roman Synkevych, Unsplash](https://unsplash.com/photos/black-and-white-penguin-toy-wX2L8L-fGeA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
