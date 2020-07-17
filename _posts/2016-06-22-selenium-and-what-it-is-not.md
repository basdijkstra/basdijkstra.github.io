---
id: 1484
title: Selenium and what it is not
date: 2016-06-22T08:00:26+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1484
permalink: /selenium-and-what-it-is-not/
categories:
  - Selenium
tags:
  - selenium webdriver
  - tool abuse
---
_Please note: this post is in no way meant as a rant towards some of my fellow testers. I&#8217;ve only written this to try and clear up some of the misconceptions with regards to Selenium WebDriver that I see popping up on various message boards and social media channels._

Selenium is hot in the test automation world. This probably isn&#8217;t news to you. If it is, you&#8217;re welcome, please remember you read it here first! In all seriousness, Selenium has been widely used for the past couple of years, and I don&#8217;t see its popularity fading any time soon. Due to its popularity, Selenium has been the go-to solution for everybody looking to write automated checks for browser-based applications, even though sometimes it might not be the actual best answer to the question at hand. In the past couple of years I have seen a lot of funny, strange and downright stupid questions being asked with regards to Selenium.

In this post, I would like to look at Selenium from a slightly different perspective by answering the question &#8216;what is Selenium NOT?&#8217;. All of the examples in the remainder of this blog post are based on actual questions or anecdotes I have read. I&#8217;m not going to link to them, since this post is not about blaming and shaming, it&#8217;s about educating those in need of some education.

**Selenium is not a test tool**  
You read that right, Selenium is not a test tool. A lot of people (including a staggeringly large percentage of recruiters) get this wrong, unfortunately. To quote the Selenium web site:

> Selenium automates browsers. That&#8217;s it! What you do with that power is entirely up to you.

My personal number one reason why Selenium is not a test tool: it does not provide a mechanism to perform assertions. For that, you&#8217;ll have to combine Selenium with JUnit or TestNG (for Java), NUnit (for C#) or any other actual (unit) testing framework. Selenium only performs predefined actions on a web site or other application running in a browser. It does this fairly well, but that doesn&#8217;t make it a test tool.

<div id="attachment_1485" style="width: 610px" class="wp-caption aligncenter">
  <a href="http://www.ontestautomation.com/?attachment_id=1485" rel="attachment wp-att-1485"><img aria-describedby="caption-attachment-1485" src="http://www.ontestautomation.com/wp-content/uploads/2016/06/selenium-testing-tool.gif" alt="No, Selenium is NOT a test tool" width="600" height="450" class="size-full wp-image-1485" /></a>
  
  <p id="caption-attachment-1485" class="wp-caption-text">
    No, Selenium is NOT a test tool
  </p>
</div>

Off-topic: the same applies to tools that support Behaviour Driven Development, such as Cucumber and SpecFlow. Those aren&#8217;t test tools either. They can be used as an assistant in writing automated tests (checks), but that isn&#8217;t the same thing.

**Selenium is not a tool to be used in API testing**  
I&#8217;ve seen this one come by a number of times, even as recently as a few days ago:

> How can I perform tests on APIs using Selenium?

Again, Selenium automates browsers, so it operates on the user interface level of an application. Actions performed on a user interface might invoke API calls. Selenium is completely oblivious to this API interaction, however. There&#8217;s no way to have Selenium interact with APIs directly. If you want to perform automated checks on the API level, please use a tool that is specifically created for these types of checks, such as <a href="http://rest-assured.io" target="_blank">REST Assured</a>. It might be wise to repeatedly ask yourself &#8216;<a href="http://www.mwtestconsultancy.co.uk/cross-browser-checking-anti-pattern/" target="_blank">Am I actually testing the user interface, or am I merely testing my application THROUGH that user interface?</a>&#8216;.

**Selenium is not a performance test tool**  
Seeing how easy it is to write tests using Selenium (I&#8217;m not saying it&#8217;s easy to write GOOD tests, though), a lot of people seem to think that these tests can easily be leveraged to execute load and performance tests as well, for example using Selenium Grid. This is a pretty bad idea, though. Selenium Grid is a means to execute your functional tests in parallel, thereby shortening the execution time. It is not meant to be (ab)used as a performance testing platform, if only for these two simple reasons:

  * It doesn&#8217;t scale very well, so you&#8217;ll probably have a hard time generating anything but trivial loads
  * Selenium does not offer a mechanism to measure response times (at least not without taking into account the time needed at the client side to render a page or specific elements), and Selenium Grid isn&#8217;t able to gather and aggregate these response times for each individual note and present them in a concise and useful manner.

Both of these are essential if you&#8217;re serious about your performance testing, so please use a tool that is specifically written for that purpose, such as <a href="http://jmeter.apache.org/" target="_blank">JMeter</a>.

**Selenium can&#8217;t handle your desktop applications**  
I feel that I&#8217;m starting to repeat myself here: Selenium automates browsers. Anything outside the scope of that browser cannot be handled by Selenium. This means that Selenium also can&#8217;t handle alerts and dialogs that are native to the operating system, such as the Windows file upload/download dialogs. To handle these, either use a tool such as <a href="https://www.autoitscript.com/site/autoit/" target="_blank">AutoIt</a> (if you&#8217;re on Windows) or, and this approach is much preferred, bypass the user interface altogether and use a solution such as the one presented in <a href="http://ardesco.lazerycode.com/index.php/2012/07/how-to-download-files-with-selenium-and-why-you-shouldnt/" target="_blank">this blog post</a>.

I sincerely hope this clears up some of the misconceptions around Selenium. If you have any other examples, feel free to post a comment or send me an email.