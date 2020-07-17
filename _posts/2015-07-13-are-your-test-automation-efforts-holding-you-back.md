---
id: 948
title: Are your test automation efforts holding you back?
date: 2015-07-13T13:03:33+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=948
permalink: /are-your-test-automation-efforts-holding-you-back/
categories:
  - Best practices
  - General test automation
tags:
  - best practice
  - organizational benefits
  - test automation
---
In case you either have lived under a rock when it comes to testing trends (I don&#8217;t say you did), you haven&#8217;t paid any attention to what I&#8217;ve written in the past (this is much more likely), or possibly both, it seems that everybody in the testing world sort of agrees that automated testing can be highly beneficial or even essential to software development project success. Especially with ever shortening release cycles and less and less time per cycle available for testing.

But why are so many hours spent on creating automated tests nothing more than a waste of time? In this post, I would like to sum up a couple of reasons that cause automated test efforts to go bad. These are reasons I&#8217;ve seen in practice, that I had to work with and improve upon. And yes, I have certainly been guilty of doing some of the things I&#8217;ll mention in this post myself. That&#8217;s how I learned &#8211; the hard way.

**Your flaky tests are sending mixed signals**  
One of the most prominent causes of time wasted with test automation is what I like to call &#8216;flaky test syndrome&#8217;. These are tests that pass some of the time and fail at other times. As a result, your test results cannot be trusted upon no matter what, which effectively renders your tests useless. There is a multitude of possible reasons that can cause your tests to become flaky, the most important being timing or synchronization issues. For example: a result is not stored in the database yet when a test step tries to check it, or an object is not yet present on a web page when your test tries to access it. There are a lot of possible solutions to these problems, and I won&#8217;t go into detail on every one of them, but what is paramount to test automation success is that your test results should be 100% trustworthy. That is, if a test fails, it should be due to a defect in your AUT (or possibly to an unforeseen test environment issue). Not due to you using Thread.sleep() throughout your tests.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/07/fail.jpg" alt="fail" width="498" height="280" class="aligncenter size-full wp-image-956" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/07/fail.jpg 498w, https://www.ontestautomation.com/wp-content/uploads/2015/07/fail-300x169.jpg 300w" sizes="(max-width: 498px) 100vw, 498px" />](http://www.ontestautomation.com/wp-content/uploads/2015/07/fail.jpg)

**Your test results take too long to analyze**  
There is no use in having an automated test suite that is running all smoothly when your test results can&#8217;t be interpreted in a glance. If you can&#8217;t see in five seconds whether a test run is successful, you might want to update your reporting strategy. Also, if you can&#8217;t determine at least the general direction to the source of an error from your reporting in a couple of seconds, you might want to update your reporting strategy. If your general response to a failure appearing in your test reports is to rerun the test and start debugging or analyzing from there, you might want to update your reporting strategy.

**You put too much trust put in your automated tests and test results**  
Even though I just stated that you should do everything within your power to make your automated tests as trustworthy as possible, this does by no means imply that you should trust on your automated tests alone when it comes to product quality. Automated tests (or better, checks) can only do so much in verifying whether your application meets all customer demands. So, for all of you having to deal with managers telling you to automate all of the tests, please, please do say NO.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/07/Test_All_The_Things.png" alt="Please don&#039;t do this!" width="400" height="600" class="aligncenter size-full wp-image-958" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/07/Test_All_The_Things.png 400w, https://www.ontestautomation.com/wp-content/uploads/2015/07/Test_All_The_Things-200x300.png 200w" sizes="(max-width: 400px) 100vw, 400px" />](http://www.ontestautomation.com/wp-content/uploads/2015/07/Test_All_The_Things.png)

**You test everything through the user interface**  
I don&#8217;t think I have to tell this again, especially not to those of you that are regular readers of my blog, but I&#8217;m fairly skeptical when it comes to GUI-driven test automation. If anything, it makes your test runs take longer and it will probably result in more maintenance efforts as a result of any UI redesign. Setting up a proper UI-based automation framework also tends to take longer, so unless you&#8217;re really testing the user interface (which is not that often the case), please try and avoid using the UI to execute your tests as much as possible. It might take a little longer to figure out how to perform a test, but it will be much more efficient in the longer run.

Hopefully these points will trigger you to take a critical look at what you&#8217;re doing at any point in your test automation efforts. What also might help is to ask yourself what I think is the most important question when it comes to test automation (and to work in general, by the way):

> &#8220;Am I being effective, or am I just being busy?&#8221;

Let&#8217;s make test automation better.