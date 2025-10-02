---
layout: page
title:  "Testing in Jenkins"
cover-img: /assets/img/jenkins_splash.jpg
date:   2025-09-14 13:14:31 +0100
author: Dan Caseley
---

## Introduction

This guide is intended for XMPP Server developers who are building in Jenkins. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. This assumes that you already have a pipeline established, and wish to add the additional checks.

Rather than create an additional plugin that you need to install and manage in Jenkins, and that we'd need to maintain with the latest bundle of tests, we've instead provided example Jenkinsfile matter for you to integrate into your own pipeline.

Assuming you already have a build pipeline and a scriptable method of launching your server, adding our interop tests is as easy as launching a container:

{% highlight groovy %}

stage('test-server') {
    steps {
        sh "docker run \
                ghcr.io/xmpp-interop-testing/xmpp_interop_tests:v1.6.1 \
                --domain=shakespeare.lit \
                --adminAccountUsername=juliet \
                --adminAccountPassword=O_Romeo_Romeo!"
    }
}

{% endhighlight %}

This example demonstrates one possible way to provision accounts. It assumes your server is running and accessible at `shakespeare.lit`, and that it already has an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. Other configuration methods are also available.

For the full list of options available to the docker image, including how to persist logs, limit the scope of the tests, or explicitly set a host rather than relying on DNS, see the [Docker](/documentation/docker) instructions.

## A Full Example

{% highlight groovy %}

pipeline {
    agent any
    environment {
        INTEROP_TESTS_VERSION = 'v1.6.1'
    }
    stages {
        stage('build-server') {
            steps {
                sh "./mvnw package"
            }
        }
        stage('run-server') {
            steps {
                sh "./my_xmpp_server/launch-my-server.sh"
            }
        }
        stage('test-server') {
            steps {
                sh "docker pull ghcr.io/xmpp-interop-testing/xmpp_interop_tests:$INTEROP_TESTS_VERSION"
                sh "docker run \
                        -v ./xmpplogs:/logs \
                        ghcr.io/xmpp-interop-testing/xmpp_interop_tests:$INTEROP_TESTS_VERSION \
                        --domain=shakespeare.lit \
                        --host=host.docker.internal \
                        --timeout=5000 \
                        --domain=shakespeare.lit \
                        --adminAccountUsername=juliet \
                        --adminAccountPassword=O_Romeo_Romeo! \
                        --enabledTests='XEP-0060'"
            }
        }
    }
    post {
        always {
            sh './my_xmpp_server/stop-my-server.sh || true'
            cleanWs()
        }
    }
}

{% endhighlight %}

In addition to the first example, this shows:

- a trivialised build & launch routine (assumes that the launch script ends when the server is ready to accept connections)
- an environment variable to parameterise the version of the tests in use
- an explicit docker pull, to separate network failures from test failures
- log preservation into a local folder (that'll be created on use)
- use of the `host` option to direct the resolution of shakespeare.lit to the Jenkins runner
- a configuration that enables just XEP-0060 tests for PubSub
- a tidy termination of the server (which is more important if the server itself is containerised)

## See also

- [Our Docker docs](/documentation/docker) for configuration options
- [Real Jenkinsfile](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-jenkins/blob/main/Jenkinsfile) that uses an Openfire release

<!-- markdownlint-disable-next-line MD036 -->
_Splash image courtesy of Dan, who saw this pub whilst shopping for pie_
