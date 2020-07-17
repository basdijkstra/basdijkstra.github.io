---
id: 1806
title: On false negatives and false positives
date: 2017-03-08T08:00:19+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1806
permalink: /on-false-negatives-and-false-positives/
categories:
  - General test automation
tags:
  - confidence
  - false negatives
  - false positives
  - trust
---
In <a href="http://www.ontestautomation.com/trust-automation/" target="_blank">a recent post</a>, I wrote about how trust in your test automation is needed to create confidence in your system under test. In this follow up post (of sorts), I&#8217;d like to take a closer look at two phenomena that can seriously undermine this trust: false positives and false negatives.

**False positives**  
Simply put, false positives are test instances that fail without there being a defect in the application under test, i.e., the test itself is the reason for the failure. False positives can occur for a multitude of reasons, including:

  * No appropriate waiting is implemented for an object before your test (for example written using Selenium WebDriver) is interacting with it.
  * You specified incorrect test data, for example a customer or an account number that is (with reason) not present in the application under test.

False positives can be really annoying. It takes time to analyze their root cause, which wouldn&#8217;t be so bad if the root cause was in the application under test, but that would be an actual defect, not a false positive. The minutes (hours) spent getting to the root cause of tests that fail because they&#8217;ve been poorly written would almost always have been better spent otherwise, on writing stable and better performing tests in the first place, for example.

If they&#8217;re part of an automated build and deployment process, you can find yourself in even bigger trouble with false positives. They stall your build process unnecessarily, thereby delaying deployments that your customers or other teams are waiting for.

There&#8217;s also another risk associated with false positives: when they&#8217;re not taken care of as soon as possible, people will start taking them for granted. Tests that regularly or consistently cause false positives will be disregarded and, ultimately, left alone to die. This is a real waste of the time it took to create that test in the first place, I&#8217;d say. I&#8217;m all for removing tests from your test base if they no longer serve a purpose, but removing them just because they fail either intermittently or every time is NOT a good reason to discard them.

And talking about tests failing intermittently, those (also known as flaky tests) are the worst, simply because the root cause for their failure often cannot be easily determined, which in turn makes it hard to fix them. As an example: I&#8217;ve seen tests that ran overnight and failed sometimes and passed on other occasions. It took weeks before I found out what caused them to fail: on some test runs this particular test (we&#8217;re talking about an end to end test that took a couple of minutes to run here) was started just before midnight, causing funny behavior in subsequent steps that were completed after midnight (when a new day started). On other test runs, either the test started after midnight or was completed before midnight, resulting in a &#8216;pass&#8217;. Good luck debugging that during office hours!

**False negatives**  
While false positives can be really annoying, the true risk with regards to trust in test automation is at the other end of the unreliability spectrum: enter false negatives. These are tests that pass but shouldn&#8217;t, because there IS an actual defect in the application under test, it&#8217;s just not picked up by the test(s) responsible for covering the area of the application where the defect occurs.

False negatives are far more dangerous than false positives, since they instill a false sense of confidence in the quality of your application. You think you&#8217;ve got everything covered with your test set, and all lights are green, but there&#8217;s still a defect (or two, or ten, or &#8230;) that goes unnoticed. And guess who&#8217;s going to find them? Your users. Which is exactly what you thought you were preventing by writing your automated regression tests.

Detecting false negatives is hard, too, since they don&#8217;t let you know that they&#8217;re there. They simply take up their space in your test set, running without trouble, never actually catching a defect. Sometimes, these false negatives are introduced at the time of writing the test, simply because the person responsible for creating the tests isn&#8217;t paying attention. Often, though, false negatives spring into life over time. Consider the following HTML snippet, representing a web page showing an error message:

<pre class="brush: html; gutter: false">&lt;html&gt;
	&lt;body&gt;
		...
		&lt;div class="error"&gt;Here&#039;s a random error message&lt;/div&gt;
		...
	&lt;/body&gt;
&lt;/html&gt;</pre>

One of the checks you&#8217;re performing is that no error messages are displayed in a happy path test case:

<pre class="brush: java; gutter: false">@Test(description="Check there is no error message when login is successful")
public void testSuccessfulLogin() {
		
	LoginPage lp = new LoginPage();
	HomePage ep = lp.correctLogin("username", "password");
	Assert.assertTrue(hp.hasNoErrorText());
}

public bool hasNoErrorText() {

	return driver.findElements(By.className("error")).size() == 0;
}</pre>

Now consider the event that at some point in time, your developer decides to mix up class annotations (not a highly unlikely event!), which results in a new way of displaying error messages:

<pre class="brush: html; gutter: false">&lt;html&gt;
	&lt;body&gt;
		...
		&lt;div class="failure"&gt;Here&#039;s a random error message&lt;/div&gt;
		...
	&lt;/body&gt;
&lt;/html&gt;</pre>

The aforementioned test will still run without trouble after this change. The only problem is that its defect finding ability is gone, since it wouldn&#8217;t notice in case an error message IS displayed! Granted, this might be an oversimplified example, and a decent test set would have additional tests that would fail after the change (because they expect an error message that is no longer there, for example), but I hope you can see where I&#8217;m going with this: false negatives can be introduced over time, without anyone knowing.

So, how to reduce (or even eliminate) the risk of false negatives? If you&#8217;re dealing with unit tests, I highly recommend experimenting with <a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/" target="_blank">mutation testing</a> to assess the quality of your unit test set and its defect finding ability. For other types of automated checks, I recommend the practice of <a href="http://www.ontestautomation.com/do-you-check-your-automated-checks/" target="_blank">checking your checks</a> regularly. Not just at the time of creating your tests, though! Periodically, set some time aside to review your tests and see if they still make sense and still possess their original defect finding power. This will reduce the risk of false negatives to a minimum, keeping your tests fresh and preserving trust in your test set and, by association, in your application under test. And isn&#8217;t that what testing is all about?