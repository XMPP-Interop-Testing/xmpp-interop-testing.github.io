---
layout: page
title:  "Testing in Bamboo"
cover-img: /assets/img/bamboo-ci_splash.png
date:   2024-06-11 14:13:52 +0200
author: Guus der Kinderen
---

## Introduction

This guide is intended for XMPP server developers who are building their server in Atlassian Bamboo. It demonstrates how to add XMPP Interoperability and Standards Conformance tests integrated into a build & test plan. This assumes that you already have a plan established, and wish to add the additional checks.

The checks run in a to-be-added task within the plan. The task type that will be used is provided by a custom Bamboo plugin, developed by us. The task will execute tests and output junit-esque and debugging output. The only prerequisite is that you've got a built server and have started it.

The following example plan configuration will be used as a starting point for this guide. It has two stages:
- a 'build' stage that creates the server software
- a 'test' stage, in which some pre-existing tests are configured.

This guide leads to a new job being added to the test stage, in which the XMPP Interop Tests are executed.

{% include image.html url="/assets/img/bamboo-config-example-project.png" description="Figure 1. An example Bamboo project configuration" %}

Note that your project configuration can be very different from this example. Please treat it as just that: an example.

## Installing the Plugin

Download the latest release of the [XMPP Interop Testing plugin for Bamboo](https://github.com/XMPP-Interop-Testing/xmpp-interop-tests-bamboo-plugin), which should be a JAR-file.

Following the [documentation provided by Atlassian](https://confluence.atlassian.com/bamboo/installing-a-plugin-289277265.html), install the plugin in your Bamboo instance.

{% include image.html url="/assets/img/bamboo-config-install-plugin.png" description="Figure 2. Install the Bamboo plugin" %}


## Installing the Test Executable

The XMPP Interop Testing plugin for Bamboo that you've installed in the previous step provides the framework for test execution, but does not include the tests themselves. The tests are installed as a Bamboo "Executable capability".

Download the latest release of the [Smack SINT Server Extensions](https://github.com/XMPP-Interop-Testing/smack-sint-server-extensions/releases) project, which should be a JAR-file. This is the file that will be added as a Bamboo Executable capability.

Our Bamboo plugin, that you've installed earlier, will have made available a new "Executable capability" type, named "Smack SINT Server Extensions JAR". Following these guidelines in [Atlassian's documentation](https://confluence.atlassian.com/bamboo/defining-a-new-executable-capability-289277164.html), add the JAR file that you downloaded as a new Capability of the type "Executable", that uses that Executable Type. Choose a recognizable label, which typically refers to the version number of the JAR file that you used.

{% include image.html url="/assets/img/bamboo-config-server-executable.png" description="Figure 3. Adding Smack SINT Server Extensions as a Server Capability" %}

## Ensure that a suitable JDK is available

The test execution requires that Java version 11 or higher is available. Ensure that your server has an appropriate JDK installed as a server capability. Refer to [Atlassian's documentation on Defining a new JDK capability](https://confluence.atlassian.com/bamboo/defining-a-new-jdk-capability-289277157.html) for details on how to achieve this.

## Executing the tests

In our example plan configuration, we'll add a new job to the Test Stage. This job will have two tasks:
- one that starts the XMPP server that has been generated in the previous stage. This guide assumes that you know how to do this for your project. The goal of this job is to have a running server, that is network accessible, to be used by the next job.
- a task that executes the XMPP Interop Tests.
- (you might need a 'final' task to cleanly shut down the server that was started)

For the purpose of this guide, we will focus on the second task.

In the screenshot below, the second task is being added to a job that already has a 'start' and 'stop' task. The Bamboo plugin that was installed earlier has made available a Task type named "XMPP Interop Testing" (in the category "Tests"). This is the task type that needs to be selected.

{% include image.html url="/assets/img/bamboo-config-add-xmppinteroptesting-task.png" description="Figure 4. Adding The XMPP Interop Testing task" %}

When configuring the test, ensure that under the "Smack SINT Server Extensions JAR" option, the Test Executable is selected that was created earlier in this guide.

The Build JDK that is selected needs to be Java 11 or higher.

To interact with the server, fill out the IP address (or hostname) of the server that was started in the previous job. Also provide the name of the XMPP domain that is serviced by that server. When the server is running on local hardware, then there should not be a reason to increase the default timeout value.

The tests are executed using dedicated accounts. These accounts are created by the test framework in one of two ways:
- by using an administrative account (per [XEP-0133](https://xmpp.org/extensions/xep-0133.html))
- using in-band registration (per [XEP-0077](https://xmpp.org/extensions/xep-0077.html))

If the former method is desired, then you should provide the credentials of an administrative user in the task configuration. When these credentials are not provided, then the latter method will be used to provision test accounts.

Finally, you can control the tests that are to be attempted. You can provide a comma-separated list of tests that are to be skipped (For example: `EntityCapsTest,SoftwareInfoIntegrationTest`), or specifications (not case-sensitive) that are to be skipped (For example: `XEP-0045,XEP-0060`). Conversely, you can limit the run tests by providing a comma-separated list of tests or specifications to enabled (for example, for quick tests of refactored functionality, or to test functionality offered in a server with a plugin architecture). If both the enabled and disabled lists are provided, then tests will only run that match the enabled list, and are not in the disabled list. For more information on enabling and disabling tests, consult [this guide](/documentation/selecting-tests).

{% include image.html url="/assets/img/bamboo-config-edit-xmppinteroptesting-task.png" description="Figure 5. Configuring The XMPP Interop Testing task" %}

{% include doc-next-steps.html %}

_Splash image courtesy of [Atlassian](https://wac-cdn.atlassian.com/dam/jcr:31f25b51-d53c-4fda-ad9c-7c150d9cfefa/illustrations-spot-hero-Software%20Development_1.svg?cdnVersion=1807)_

