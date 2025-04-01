---
layout: page
title:  "Choosing what test to run"
cover-img: /assets/img/selecting_tests_splash.jpg
date:   2024-05-15 21:43:29 +0200
author: Guus der Kinderen
---

This page shows how you can include or exclude tests from the execution of your runner. When you exclude tests, all tests except
for the excluded tests are executed. Similarly, when you include tests, only those tests are executed.

**Note**: It is currently not possible to select _individual_ tests. Test can only be selected in context of a larger set.
More on that below.

## Selecting tests By Specification

A set of tests can be identified by specification. Typically, an RFC or XEP reference is used, such as these examples:

- `RFC6121`
- `XEP-0060`

## Selecting tests By Name

An alternative approach is to select a set by the name of their implementation. For a list of names, you can have a peek
in the output logs of a test execution, which will list the names of the tests that are being executed. See, for
example, this snippet of an output log:

```plain
May 15, 2024 5:46:24 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: IoTDataIntegrationTest.dataTest (Normal) Start
May 15, 2024 5:46:25 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: IoTDataIntegrationTest.dataTest (Normal) Success
May 15, 2024 5:46:25 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: MultiUserChatIntegrationTest.mucTest (Normal) Start
May 15, 2024 5:46:25 PM org.igniterealtime.smack.inttest.SmackIntegrationTestFramework runConcreteTest
INFO: MultiUserChatIntegrationTest.mucTest (Normal) Success
```

In this snippet, two test names are mentioned:
- `IoTDataIntegrationTest` 
- `MultiUserChatIntegrationTest`

## Configuring tests

Every test runner has four configuration options that can be used to include or exclude tests. All of these are optional. You can
use them with one or more values, if so desired (unless otherwise stated in the runner-specific documentation, you can
use comma-separated values to provide more than one value for an option).

- `disabledTests` - used to configure names of tests that are to be skipped. For example: `IoTDataIntegrationTest,MultiUserChatIntegrationTest`
- `disabledSpecifications` - A set of specifications (not case-sensitive) that are to be skipped. For example: `XEP-0045,XEP-0060`
- `enabledTests` - used to configure names of tests that are to be included.
- `enabledSpecifications` - A set of specifications (not case-sensitive) that are to be included.

If all options are blank, then all tests are executed.

If an option to both enable and disable tests is provided, then both apply - tests will only run that match the enabled list, and are not in the disabled list. (An example of where this might be useful: You want to test the implementation of a specification but know that your implementation doesn't include a feature and so you disable certain tests.)

That's all there is to it!

_Splash image courtesy of [Rima Kruciene, Unsplash](https://unsplash.com/photos/black-and-white-audio-controller-close-up-photo-gpKe3hmIawg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)_
