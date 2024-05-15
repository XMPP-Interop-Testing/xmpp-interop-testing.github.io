---
layout: page
title:  "Diagnosing Test Failures"
cover-img: /assets/img/diagnose_splash.jpg
date:   2024-05-15 14:32:31 +0200
author: Guus der Kinderen
---

An inevitable result of running tests is that sometimes, the tests fail. Your continuous integration runner displays a
big red cross, notifications are sent out informing everyone that "the build has failed!" - but now what?

Finding the cause of a test failure can be tricky. This document will guide you on how to approach this.

## Finding the cause of test failures

When running the tests, various log files are created. We will primarily use these to identify the source of the 
problem.

The three-staged approach to finding the cause of the test failure is as follows
1. Identifying the test(s) that fail
2. Referencing the XMPP traffic
3. Identifying the problem

Strap in, here we go!

### Identifying the test(s) that fail

Although some runners provide more information (such as a JUnit-based test report) all runners will at least show the 
status of tests that have executed in their console output. 

A considerable part of the logs will be lines that show that a test has started, and that it concluded, either successfully:
```
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: ServiceDiscoveryIntegrationTest.testQueryInfoNonExistingBareJidClientEntityWithNode (Normal) Start
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: ServiceDiscoveryIntegrationTest.testQueryInfoNonExistingBareJidClientEntityWithNode (Normal) Success
```

... or unsuccessfully:

```
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal) Start
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
SEVERE: ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal) Failed
```

Near the end of the log file, the console will print how many tests were executed, and how many of those tests failed:

> INFO: SmackIntegrationTestFramework[muy28] finished: 127/136 [8 failed]

When there are test failures, these are then iterated after this line:

> WARNING: 💀 The following 8 tests failed! 💀

This will be followed by an iteration of names of the tests that failed, combined with a stacktrace that contains the
assertion that failed, such as this one:

```
May 15, 2024 5:46:45 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework$JulTestRunResultProcessor process
SEVERE: ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal) failed: org.opentest4j.AssertionFailedError: Expected the disco#info request from 'smack-inttest-one-muy28@example.org/one-muy28' to 'smack-inttest-two-muy28@example.org' (which has not granted presence subscription to the requestor) to be responded to with an error (but it was not).
org.opentest4j.AssertionFailedError: Expected the disco#info request from 'smack-inttest-one-muy28@example.org/one-muy28' to 'smack-inttest-two-muy28@example.org' (which has not granted presence subscription to the requestor) to be responded to with an error (but it was not).
	at org.junit.jupiter.api.AssertionUtils.fail(AssertionUtils.java:39)
	at org.junit.jupiter.api.Assertions.fail(Assertions.java:109)
	at org.igniterealtime.smack.inttest.xep0030.ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode(ServiceDiscoveryIntegrationTest.java:371)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework.lambda$runTests$0(SmackIntegrationTestFramework.java:476)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework.runConcreteTest(SmackIntegrationTestFramework.java:551)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework$PreparedTest.run(SmackIntegrationTestFramework.java:759)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework.runTests(SmackIntegrationTestFramework.java:539)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework.run(SmackIntegrationTestFramework.java:277)
	at org.igniterealtime.smack.inttest.SmackIntegrationTestFramework.main(SmackIntegrationTestFramework.java:115)
```

From the first two lines of the above, two key bits of information can be deduced:

1. The name of the test that failed: `ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal)`
2. The assertion that failed is: 
   > Expected the disco#info request from 'smack-inttest-one-muy28@example.org/one-muy28' to 'smack-inttest-two-muy28@example.org' (which has not granted presence subscription to the requestor) to be responded to with an error (but it was not).

Finally, at the end of log, there a summary is provided of the specifications that correlate to the failed tests:

```
SEVERE: The failed tests correspond to the following specifications:
- XEP-0030 (version 2.5.0) section 8:	"The following rule[s] apply to the handling of service discovery requests sent to bare JIDs: In response to a disco#info request, the server MUST return a <service-unavailable/> error if [...] [t]he requesting entity is not authorized to receive presence from the target entity." (as tested by 'ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal)')
```

Each line contains these bits of information:
- A reference to the specification (and section) identifier: `XEP-0030 (version 2.5.0) section 8`
- A quote of the specification that in more detail describes the functionality that is subject of the (failed) test:
  > The following rule[s] apply to the handling of service discovery requests sent to bare JIDs: In response to a disco#info request, the server MUST return a <service-unavailable/> error if [...] [t]he requesting entity is not authorized to receive presence from the target entity.
- The name of the test that failed: `(as tested by 'ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal)')`

### Referencing the XMPP traffic

Besides the console output, the test runner will also generate a log file per test. These log files contain the raw XMPP
data that is exchanged with the server.

Each test will log its traffic in a distinct file. The name of the file matches the name of the test, appended with the
`.log` suffix. These files are stored in a directory that, unless otherwise configured, is named `logs` and can be found
in the working directory of the test execution.

For the failed test that is used in the example above, the XMPP data will be stored in a file named 
`ServiceDiscoveryIntegrationTest.testQueryInfoWithoutPresenceSubscriptionBareJidWithoutNode (Normal).log`

The log files are typically quite full of data, as most tests generate quite a bit of XMPP traffic. The composition of
each file is the same. Each file contains of fragments like these two taken from the log file that we were using as an
example:

```xml
17:46:27.591 SENT (1): 
<iq to='smack-inttest-two-muy28@example.org' id='41R2Q-110' type='get'>
  <query xmlns='http://jabber.org/protocol/disco#info'>
  </query>
</iq>
17:46:27.637 RECV (1): 
<iq type="result" id="41R2Q-110" from="smack-inttest-two-muy28@example.org" to="smack-inttest-one-muy28@example.org/one-muy28">
  <query xmlns="http://jabber.org/protocol/disco#info">
    <identity category="account" type="registered"/>
    <feature var="http://jabber.org/protocol/disco#info"/>
  </query>
</iq>
```

The first line of the fragment:
- starts with a timestamp,
- followed by either `SENT` or `RECV`, indicating if the client has transmitted or received data.
- Some tests use more than one client connection. To differentiate between data of the different clients, the last few
  characters of the first line provide a numeric identifier (typically `1`, `2` or `3`) that is a unique identifier of
  the client that transmitted or received the data.

All other lines of the fragment is an XML element that typically represents the XMPP data that was sent or received. 
Note that formatting has been applied to the XML data. You're not seeing the data _exactly_ as-is transmitted on the
wire. 

### Identifying the problem

Based on all the information above, we can conclude that the server that is being tested does not comply with
[XEP-0030 'Service Discovery', section 8 'Security Considerations'](https://xmpp.org/extensions/xep-0030.html#security).
Specifically, this part of that section was the subject of the test:

> The following rule[s] apply to the handling of service discovery requests sent to bare JIDs: In response to a 
> disco#info request, the server MUST return a <service-unavailable/> error if [...] [t]he requesting entity is not
> authorized to receive presence from the target entity.

The failed assertion was recorded as:

> Expected the disco#info request from 'smack-inttest-one-muy28@example.org/one-muy28' to 
> 'smack-inttest-two-muy28@example.org' (which has not granted presence subscription to the requestor) to be responded 
> to with an error (but it was not).

We can now cross-reference that with the XMPP data that was exchanged with the server:

```xml
17:46:27.591 SENT (1): 
<iq to='smack-inttest-two-muy28@example.org' id='41R2Q-110' type='get'>
  <query xmlns='http://jabber.org/protocol/disco#info'>
  </query>
</iq>
17:46:27.637 RECV (1): 
<iq type="result" id="41R2Q-110" from="smack-inttest-two-muy28@example.org" to="smack-inttest-one-muy28@example.org/one-muy28">
  <query xmlns="http://jabber.org/protocol/disco#info">
    <identity category="account" type="registered"/>
    <feature var="http://jabber.org/protocol/disco#info"/>
  </query>
</iq>
```

Sure enough, we can see that connection 1 _sent_ a `disco#info` request, and received a response. The response that was
received was _not_ the expected `<service-unavailable/>` error, indicating that indeed, there is a problem with
compliance to the XMPP standard!

## Look at the implementation of the test

Up until now, we've tried to steer you away from having to analyze the test implementation itself. Our reasoning is that
not everyone is willing or able to dig through the mostly Java code that make up our test implementations. That's why
we've put effort in making available the logs and documentation above: ideally, anyone that's familiar with XMPP and its
specifications, should be able to deduce from those what's going wrong - even if you're not familiar with Java, Smack,
and/or our test implementations . However, much information can be found in the test implementation code. You can use
the pointers in this section to guide you.

The primary test framework that is used by this project is the Smack Integration Test framework - SINT for short. More
information about this project can be found in [its documentation](https://download.igniterealtime.org/smack/dailybuilds/sinttest-javadoc/org/igniterealtime/smack/inttest/package-summary.html).

The test implementation are sourced from two different repositories:

1. **Smack's Source code** contains the original set of 'integration tests', as well as the implementation of the test 
   framework itself. The implementation of individual tests is spread out throughout the codebase of Smack. They can be
   identified by looking at their class name, that typically ends with `IntegrationTest`. You'll find the source code of
   Smack in [Smack's GitHub Repository](https://github.com/igniterealtime/Smack/).
2. **Smack SINT Server Extensions** contain additional tests. The tests that are included in Smack are primarily 
   intended to test the functionality of Smack itself, not so much the server that is used. Server-oriented tests are
   added to this project. The sources can be found in 
   [Smack SINT Server Extensions' GitHub Repository](https://github.com/XMPP-Interop-Testing/smack-sint-server-extensions). 

## Consider that the test is wrong

Last, but not least: implementing tests is _hard_. Harder than many people imagine. There is a distinct possibility that
a test fails not because of a problem in your server, but in the implementation of our test. If a test fails, and you
have reason to believe that this is caused by a bug in the test implementation rather than the server that you're 
testing, please reach out to us!

_Splash image courtesy of [Andres Siimon, Unsplash](https://unsplash.com/photos/man-in-red-and-white-checkered-dress-shirt-wearing-black-fedora-hat-Oe3JidQ9UvU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
