---
layout: page
title:  "Testing in Docker"
cover-img: /assets/img/docker_splash.jpg
date:   2024-08-21 10:55:59 +0200
author: Guus der Kinderen
---

This guide is intended for XMPP Server developers who are building their server using a continuous integration solution that is capable of launching Docker instances.

The Docker-based approach has been created for those development environments that don't match [any of our other, more specific CI-integrations](/index#installation--configuration), but it none-the-less offers the full range of features.

This guide demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. It is assumed that you already have a pipeline established, and wish to add the additional checks provided by our project.

Docker container images for the XMPP Interop Testing project are published to [the GitHub Package / Container registry of our project](https://github.com/orgs/XMPP-Interop-Testing/packages). Using them can be as simple as this:

{% highlight bash %}
docker pull ghcr.io/xmpp-interop-testing/xmpp_interop_tests:latest

docker run \
    --network=host \
    ghcr.io/xmpp-interop-testing/xmpp_interop_tests:latest \
    --domain=shakespeare.lit \
    --adminAccountUsername=juliet \
    --adminAccountPassword=O_Romeo_Romeo
{% endhighlight %}

The above assumes that your server is running and reachable on the domain `shakespeare.lit` and that it is provisioned with an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. You'll find the full range of available configuration options below.

## Configuration

Various options are available when invoking the _xmpp_interop_tests_, and whilst none of them are absolutely required, the defaults are unlikely to be perfect for everyone.

| Option                 | Description                                                                                                                                                                                                                                                                           | Default value       |
|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| host                   | IP address or DNS name of the XMPP service to run the tests on.                                                                                                                                                                                                                       | 127.0.0.1           |
| domain                 | the XMPP domain name of server under test.                                                                                                                                                                                                                                            | example.org         |
| timeout                | the amount of milliseconds after which an XMPP action (typically an IQ request) is considered timed out.                                                                                                                                                                              | 5000 (five seconds) |
| adminAccountUsername   | (optional) The account name of a pre-existing user that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html). If not provided, in-band registration ([XEP-0077](https://xmpp.org/extensions/xep-0077.html)) will be used to provision accounts | -                   |
| adminAccountPassword   | (optional) The password of the admin account                                                                                                                                                                                                                                          | -                   |
| disabledTests          | (optional) A comma-separated list of tests that are to be skipped. For example: EntityCapsTest,SoftwareInfoIntegrationTest                                                                                                                                                            | -                   |
| disabledSpecifications | (optional) A comma-separated list of specifications (not case-sensitive) that are to be skipped. For example: XEP-0045,XEP-0060                                                                                                                                                       | -                   |
| enabledTests           | (optional) A comma-separated list of tests that are the only ones to be run. For example: EntityCapsTest,SoftwareInfoIntegrationTest                                                                                                                                                            | -                   |
| enabledSpecifications  | (optional) A comma-separated list of specifications (not case-sensitive) that are the only ones to be run. For example: XEP-0045,XEP-0060                                                                                                                                                       | -                   |

For more information on enabling and disabling tests, consult [this guide](/documentation/selecting-tests).

### Exposing XMPP traffic logs

XMPP traffic is logged by the container in its `/logs` directory. To easily access the XMPP traffic logs, use a bind mount from anywhere on the host machine into the container on the `/logs` path.

For example, by adding `-v "$(pwd)"/xmpplogs:/logs`, a directory named `xmpplogs` (relative to the working directory) on the host is used as a mount for the `/logs` directory on the container: 

{% highlight bash %}
$ docker run \
    --network=host \
    -v "$(pwd)"/xmpplogs:/logs \
    ghcr.io/xmpp-interop-testing/xmpp_interop_tests:main \
    --domain=shakespeare.lit \
    --adminAccountUsername=juliet \
    --adminAccountPassword=O_Romeo_Romeo

# Removed a lot of output for brevity.

$ ls ./xmpplogs | wc -l
255
{% endhighlight %}

### Detecting test failures

Verbose test results are logged on standard-out. To programmatically detect test failures - for example to automatically mark a build as failed - the exit code of the 'docker run' execution can be used. Any non-zero exit code indicate that tests have failed:

{% highlight bash %}
$ docker run \
    --network=host \
    -v "$(pwd)"/xmpplogs:/logs \
    ghcr.io/xmpp-interop-testing/xmpp_interop_tests:main \
    --domain=shakespeare.lit \
    --adminAccountUsername=juliet \
    --adminAccountPassword=O_Romeo_Romeo

# Removed a lot of output for brevity, but test failures were reported.

$ echo $?
2
{% endhighlight %}

For the latest updates to the documentation of the configuration updates, consult the [GitHub repository](https://github.com/XMPP-Interop-Testing/smack-sint-server-extensions?#from-a-container).

{% include doc-next-steps.html %}

_Splash image courtesy of [Bernd 📷 Dittrich, Unsplash](https://unsplash.com/photos/a-group-of-trucks-parked-next-to-each-other-in-a-parking-lot-bUnsDLFRNWc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash")_
  