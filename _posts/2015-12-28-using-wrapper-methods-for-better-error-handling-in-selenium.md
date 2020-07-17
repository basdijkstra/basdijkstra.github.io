---
id: 1197
title: Using wrapper methods for better error handling in Selenium
date: 2015-12-28T00:01:23+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1197
permalink: /using-wrapper-methods-for-better-error-handling-in-selenium/
categories:
  - Selenium
tags:
  - error handling
  - java
  - selenium webdriver
---
Frequent users of Selenium WebDriver might have come across the problem that when they&#8217;re using Selenium for testing responsive, dynamic web applications, timing and synchronization can be a major hurdle in creating useful and reliable automated tests. In my current project, I&#8217;m creating WebDriver-based automated tests for a web application that heavily relies on Javascript for page rendering and content retrieval and display, which makes waiting for the right element to be visible, clickable or whatever state it needs to be in significantly less than trivial.

Using the standard Selenium methods such as _click()_ and _sendKeys()_ directly in my script often results in failures as a result of the weg page element being invisible, disabled or reloaded (this last example resulting in a _StaleElementReferenceException_). Even using implicit waits when creating the _WebDriver_ object didn&#8217;t always lead to stable tests, and I refuse to use _Thread.sleep()_ (and so should you!). Also, I didn&#8217;t want to use individual _WebDriverWait_ calls for every single object that needed to be waited on, since that introduces a lot of extra code that needs to be maintained. So I knew I had to do something more intelligent for my tests to be reliable &#8211; and therefore valuable as opposed to a waste of time and money.

**Wrapper methods**  
The solution to this problem lies in using wrapper methods for the standard Selenium methods. So instead of doing this every time I need to perform a click:

<pre class="brush: java; gutter: false">(new WebDriverWait(driver, 10)).until(ExpectedConditions.elementToBeClickable(By.id("loginButton")));
driver.findElement(By.id("loginButton")).click();</pre>

I have created a wrapper method _click()_ in a _MyElements_ class that looks like this:

<pre class="brush: java; gutter: false">public static void click(WebDriver driver, By by) {
	(new WebDriverWait(driver, 10)).until(ExpectedConditions.elementToBeClickable(by));
	driver.findElement(by).click();
}</pre>

Of course, the 10 second timeout is arbitrary and it&#8217;s better to replace this with some constant value. Now, every time I want to perform a click in my test I can simply call:

<pre class="brush: java; gutter: false">MyElements.click(driver, By.id("loginButton");</pre>

which automatically performs a _WebDriverWait_, resulting in much stabler, better readable and maintainable scripts.

**Extending your wrapper methods: error handling**  
Using wrapper methods for Selenium calls has the additional benefit of making error handling much more generic as well. For example, if you often encounter a _StaleElementReferenceException_ (which those of you writing tests for responsive and dynamic web applications might be all too familiar with), you can simply handle this in your wrapper method and be done with it once and for all:

<pre class="brush: java; gutter: false">public static void click(WebDriver driver, By by) {
	try {
		(new WebDriverWait(driver, 10)).until(ExpectedConditions.elementToBeClickable(by));
		driver.findElement(by).click();
	catch (StaleElementReferenceException sere) {
		// simply retry finding the element in the refreshed DOM
		driver.findElement(by).click();
	}
}</pre>

This is essentially my own version of <a href="http://darrellgrainger.blogspot.nl/2012/06/staleelementexception.html" target="_blank">this</a> excellent solution for handling a _StaleElementReferenceException_. By using wrapper methods, you can easily handle any type of exception in its own specific way, which will improve your tests significantly.

**Extending your wrapper methods: logging**  
Your wrapper methods also allow for better logging capabilities. For example, if you&#8217;re using the <a href="http://extentreports.relevantcodes.com/" target="_blank">ExtentReports</a> library for creating HTML reports for Selenium tests (as I do in my current project), you can create a log entry every time an object is not clickable after the _WebDriverWait_ times out:

<pre class="brush: java; gutter: false">public static void click(WebDriver driver, By by) {
	try {
		(new WebDriverWait(driver, 10)).until(ExpectedConditions.elementToBeClickable(by));
		driver.findElement(by).click();
	catch (StaleElementReferenceException sere) {
		// simply retry finding the element in the refreshed DOM
		driver.findElement(by).click();
	}
	catch (TimeoutException toe) {
		test.log(logStatus.Error, "Element identified by " + by.toString() + " was not clickable after 10 seconds");
	}
}</pre>

Here, _test_ is the _ExtentTest_ object representing the log for the current test. See how easy this is?

**Other possible benefits**  
There&#8217;s a couple of other benefits to using these wrapper methods, some of which I use in my current project as well, such as:

  * Automatically failing the JUnit / TestNG (or in my case NUnit) test whenever a specific error occurs by including an _Assert.Fail();_
  * Restoring or recreating specific conditions or a specific application state after an error occurs to prepare for the next test (e.g. ending the current user session)
  * Automatically restarting a test in case of a specific error

And I&#8217;m sure there are many more.

Wrapping this up (pun intended!), using these wrapper methods for standard Selenium calls can be very beneficial to your Selenium experience and those of your clients or employers. They certainly saved me a lot of unnecessary code and frustration over brittle tests..