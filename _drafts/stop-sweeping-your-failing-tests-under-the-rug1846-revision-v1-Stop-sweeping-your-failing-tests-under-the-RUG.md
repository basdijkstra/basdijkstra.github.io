---
id: 1847
title: Stop sweeping your failing tests under the RUG
date: 2017-04-18T09:07:19+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1846-revision-v1/
permalink: /1846-revision-v1/
---
Hello and welcome to this week&#8217;s rant on bad practices in test automation! Today&#8217;s serving of automation bitterness is inspired by a question I saw (and could not NOT reply to) on LinkedIn. It went something like this:

> My tests are sometimes failing for no apparent reason. How can I implement a mechanism that automatically retries running the failing tests to get them to pass?

It&#8217;s questions like this that make the hairs in my neck stand on end. Instead of sweeping your test results under the RUG (Retry Until Green), how about diving into your tests and fixing the root cause of the failure?

First of all, there is ALWAYS a reason your test fails. It might be the application under test (your test did its work, congratulations!), but it might just as well be your test itself that&#8217;s causing the failure. The fact that the reason for the failure is not apparent does not mean you can simply ignore it and try running your test a second time to see if it passes then. No, it means there&#8217;s work for you to do. It might not be fun work: dealing with and catching with all kinds of exceptions that can be thrown by a Selenium test can be very tricky. The task also might not be suitable for you: maybe you&#8217;re inexperienced and therefore think &#8216;forget debugging, I&#8217;ll just retry the test, that&#8217;s way easier&#8217;. That&#8217;s OK, we&#8217;ve all been inexperienced at some point in our career. In a lot of ways, most of us still are. And I myself have not exactly been innocent of this behavior in the past either.

But at some point in time, it&#8217;s time to get over complaining about flaky tests and doing something about it. That means diving deep into your tests, how they interact with your application under test, getting to the root cause of the error or exception being thrown and fixing it, for once and for all. Here&#8217;s a real world example from a project I&#8217;m currently working on.

In my tests, I need to fill out a form to create a new savings account. Because the application needs to be sure that all information entered is valid, there&#8217;s a lot of front-end input validation going on (zip code needs to exist, email address should be formatted correctly, etc.). Whenever the application is busy validating or processing input, a modal appears that indicates to the end user that the site is busy processing input, and that therefore you should wait a little before proceeding. Sounds like a good idea, right? However, when you want your tests to fill in these forms automatically, you&#8217;ll sometimes run into the issue that you&#8217;re trying to click a button or complete a text field while it is being blocked by the modal. Cue WebDriverException (&#8220;other object would receive the click&#8221;) and failing test.

Now, there are two ways to deal with this:

  1. Sweep the test result under the RUG and retry until that pesky modal does not block your script from completing, or
  2. Catch the WebDriverException, wait until the modal is no longer there and do your _click_ or _sendKeys_ again. <a href="http://www.ontestautomation.com/using-wrapper-methods-for-better-error-handling-in-selenium/" target="_blank">Writing wrappers around the Selenium API calls</a> is a great way of achieving this, by the way.

Option 1. is the easy way. Option 2. is the right way. You choose. Just know that every failing test is trying to tell you something. Most of the time, it&#8217;s telling you to write a better test.

One more argument in favour of NOT sweeping your flaky tests under the RUG, but preventing them from happening in the future: some day, your organization might start, you know, actually relying on these test results. For example as part of a go / no go decision for deployment into production. If I were to call the shots, I&#8217;d make sure that all my tests that I rely on for making that decision were:

  * Returning reliable test results, all the time
  * Checking the right thing (but that&#8217;s <a href="http://www.ontestautomation.com/do-you-check-your-automated-checks/" target="_blank">a different post altogether</a>)

Really, it&#8217;s time to quit tolerating flaky tests. Repair them or throw them away, because what&#8217;s the added value of an unreliable test?. Just don&#8217;t sweep your failing tests under the RUG.