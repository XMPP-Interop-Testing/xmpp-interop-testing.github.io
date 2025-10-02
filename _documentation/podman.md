---
layout: page
title:  "Testing in Podman"
cover-img: /assets/img/container_splash.jpg
date:   2024-09-03 20:19:14 +0100
author: Dan Caseley
---

This guide is intended for XMPP Server developers who are building their server using a continuous integration solution that is capable of launching OCI container instances.

The container-based approach has been created for those development environments that don't match [any of our other, more specific CI-integrations](/index#installation--configuration) (or who simply prefer it), but it none-the-less offers the full range of features.

This guide demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test pipeline. It is assumed that you already have a pipeline established, and wish to add the additional checks provided by our project.

Container images for the XMPP Interop Testing project are published to [the GitHub Package / Container registry of our project](https://github.com/orgs/XMPP-Interop-Testing/packages). Using them can be as simple as this:

{% highlight bash %}
podman pull ghcr.io/xmpp-interop-testing/xmpp_interop_tests-oci:latest

podman run \
    --network=host \
    ghcr.io/xmpp-interop-testing/xmpp_interop_tests-oci:latest \
    --domain=shakespeare.lit \
    --adminAccountUsername=juliet \
    --adminAccountPassword=O_Romeo_Romeo
{% endhighlight %}

This example demonstrates one possible way to provision accounts. It assumes your server is running and accessible at `shakespeare.lit`, and that it already has an administrative account (one that is allowed to create other users, per [XEP-0133](https://xmpp.org/extensions/xep-0133.html)) that uses the username `juliet` and the provided password. Other configuration methods are also available, and you can explore the full set of options in the sections below.

## Configuration

Various options are available when invoking the tests, and whilst none of them are absolutely required, the defaults are unlikely to be perfect for everyone.

For the latest updates to the documentation of the configuration updates, consult the [GitHub repository of the OCI image](https://github.com/XMPP-Interop-Testing/smack-sint-server-extensions?#from-a-container).

{% include doc-conf-table.html %}

{% include doc-conf-provision.html %}

### Exposing XMPP traffic logs

XMPP traffic is logged by the container in its `/logs` directory. To easily access the XMPP traffic logs, use a bind mount from anywhere on the host machine into the container on the `/logs` path.

For example, by adding `-v "$(pwd)"/xmpplogs:/logs`, a directory named `xmpplogs` (relative to the working directory) on the host is used as a mount for the `/logs` directory on the container:

{% highlight bash %}
$ podman run \
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
$ podman run \
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

{% include doc-next-steps.html %}

_Splash image courtesy of [CHUTTERSNAP, Unsplash](https://unsplash.com/photos/birds-photo-of-cityscape-9cCeS9Sg6nU?utm_content=creditShareLink&utm_medium=referral&utm_source=unsplash)_
