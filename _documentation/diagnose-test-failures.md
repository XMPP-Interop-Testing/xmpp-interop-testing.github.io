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
INFO: DiscoIntegrationTest.testNonExisting (Normal) Start
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: DiscoIntegrationTest.testNonExisting (Normal) Success
```

... or unsuccessfully:

```
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: DiscoIntegrationTest.testInfoNoSub (Normal) Start
May 15, 2024 5:46:27 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
SEVERE: DiscoIntegrationTest.testInfoNoSub (Normal) Failed
```

Near the end of the log file, the console will print how many tests were executed, and how many of those tests failed:

```
Test run (id: m7te1) finished! 227 tests were successful (✔), 11 failed (💀), and 29 were impossible to run (✖).
```

This is followed by some summaries. First, a table of results aggregated by specification is shown, and next, there will
be a list of tests that were impossible to run. 

When there are test failures, these are then iterated next, after this line:

```
💀 The following 11 tests failed! 💀
```

This will be followed by a list of tests that failed. For each test failure, a block like this will be printed:

```
• XEP-0045: Multi-User Chat, Section 10.8
      "[T]he owner first requests the admin list by querying the room for all users with an affiliation of 'admin'. [...] If the <user@host> of the 'from' address does not match the bare JID of a room owner, the service MUST return a <forbidden/> error to the sender."
  Failure reason  : Unexpected error condition in the (expected) error that was returned to 'smack-inttest-two-m7te1@example.org/two-m7te1' (that is not in the room) after it requested the admin list of room 'smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org'. ==> expected: <forbidden> but was: <bad-request>
  Stanza log file : logs/MultiUserChatOwnerAdminListIntegrationTest.testUserRequestsAdminList (Normal).log
  Test class      : class org.jivesoftware.smackx.muc.MultiUserChatOwnerAdminListIntegrationTest
  Test method     : testUserRequestsAdminList
```

Each line from this block contains valuable information to help understand the test failure.

The first two lines reference the specification that defines the expected behavior:
1. On the first line, the name and (if available) the section of the specification that was being tested is shown: 
   > XEP-0045: Multi-User Chat, Section 10.8
2. This is followed by a quote of the specification that in more detail describes the functionality that is subject of the (failed) test:
   > [T]he owner first requests the admin list by querying the room for all users with an affiliation of 'admin'. [...] If the &lt;user@host> of the 'from' address does not match the bare JID of a room owner, the service MUST return a &lt;forbidden/> error to the sender.

This is followed by the _observed_ behavior:
3. The third line, starting with "Failure reason", describes the assertion that failed:
   > Failure reason  : Unexpected error condition in the (expected) error that was returned to 'smack-inttest-two-m7te1@example.org/two-m7te1' (that is not in the room) after it requested the admin list of room 'smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org'. ==> expected: &lt;forbidden> but was: &lt;bad-request>

Finally, there is a short list of debugging information:
4. Each test will generate a log file that contains the raw XMPP data (more on that below). The fourth line names the file that contains the XMPP data for the test that failed:
   > Stanza log file : logs/MultiUserChatOwnerAdminListIntegrationTest.testUserRequestsAdminList (Normal).log
5. The last two lines will contain the name of the class and method of the implementation of the test. This should be useful only when debugging the test implementation itself (and can otherwise probably be ignored):
   > Test class      : class org.jivesoftware.smackx.muc.MultiUserChatOwnerAdminListIntegrationTest
   > Test method     : testUserRequestsAdminList

### Referencing the XMPP traffic

Besides the console output, the test runner will also generate a log file per test. These log files contain the raw XMPP
data that is exchanged with the server.

Each test will log its traffic in a distinct file. The name of the file matches the name of the test, appended with the
`.log` suffix. These files are stored in a directory that, unless otherwise configured, is named `logs` and can be found
in the working directory of the test execution.

For the failed test that is used in the example above, the XMPP data is reported to be stored in a file named 
`logs/MultiUserChatOwnerAdminListIntegrationTest.testUserRequestsAdminList (Normal).log`

The log files are typically quite full of data, as most tests generate quite a bit of XMPP traffic. The composition of
each file is the same. Each file contains of fragments like these two taken from the log file that we were using as an
example:

```xml
09:15:52.49 SENT (2):
<iq to='smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org' id='HT6Q6-1264' type='get'>
    <query xmlns='http://jabber.org/protocol/muc#admin'>
        <item affiliation='admin'>
        </item>
    </query>
</iq>
09:15:52.49 RECV (2):
<iq type="error" id="HT6Q6-1264" from="smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org" to="smack-inttest-two-m7te1@example.org/two-m7te1">
<query xmlns="http://jabber.org/protocol/muc#admin">
    <item affiliation="admin"/>
</query>
<error code="400" type="modify">
    <bad-request xmlns="urn:ietf:params:xml:ns:xmpp-stanzas"/>
    <text xmlns="urn:ietf:params:xml:ns:xmpp-stanzas">
        You are not an occupant of this room.
    </text>
</error>
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
[XEP-0045: 'Multi-User Chat', Section 10.8 'Modifying the Admin List'](https://xmpp.org/extensions/xep-0045.html#modifyadmin).
Specifically, this part of that section was the subject of the test:

> [T]he owner first requests the admin list by querying the room for all users with an affiliation of 'admin'. [...] If the &lt;user@host> of the 'from' address does not match the bare JID of a room owner, the service MUST return a &lt;forbidden/> error to the sender.

The failed assertion was recorded as:

> Unexpected error condition in the (expected) error that was returned to 'smack-inttest-two-m7te1@example.org/two-m7te1' (that is not in the room) after it requested the admin list of room 'smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org'. ==> expected: &lt;forbidden> but was: &lt;bad-request>

We can now cross-reference that with the XMPP data that was exchanged with the server:

```xml
09:15:52.49 SENT (2):
<iq to='smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org' id='HT6Q6-1264' type='get'>
    <query xmlns='http://jabber.org/protocol/muc#admin'>
        <item affiliation='admin'>
        </item>
    </query>
</iq>
09:15:52.49 RECV (2):
<iq type="error" id="HT6Q6-1264" from="smack-inttest-owner-user-requests-adminlist-m7te1-jugevn@conference.example.org" to="smack-inttest-two-m7te1@example.org/two-m7te1">
<query xmlns="http://jabber.org/protocol/muc#admin">
    <item affiliation="admin"/>
</query>
<error code="400" type="modify">
    <bad-request xmlns="urn:ietf:params:xml:ns:xmpp-stanzas"/>
    <text xmlns="urn:ietf:params:xml:ns:xmpp-stanzas">
        You are not an occupant of this room.
    </text>
</error>
</iq>
```

Sure enough, we can see that connection 2 _sent_ a request for the admin list, and received a response. (The data in the
log file leading up to this fragment will show that the user on connection 2 never joined the room. That data has been
omitted here for brevity.)

The response that was received by connection 2 is an error (its text even suggests that the service properly detected
that the user that made the request was not an occupant of the room). The error condition is `<bad-request/>`, instead
of the expected `<forbidden/>`. This shows that indeed, there is a problem with compliance to the XMPP
standard!

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
testing, [please reach out to us](/contact)!

_Splash image courtesy of [Andres Siimon, Unsplash](https://unsplash.com/photos/man-in-red-and-white-checkered-dress-shirt-wearing-black-fedora-hat-Oe3JidQ9UvU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
