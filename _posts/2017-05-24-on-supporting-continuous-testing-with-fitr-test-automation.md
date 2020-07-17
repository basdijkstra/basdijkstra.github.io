---
id: 1870
title: On supporting Continuous Testing with FITR test automation
date: 2017-05-24T08:00:30+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1870
permalink: /on-supporting-continuous-testing-with-fitr-test-automation/
categories:
  - General test automation
tags:
  - continuous testing
  - test automation
  - trust
---
Last week I had the chance to participate as a contributor to my very first webinar. The people at <a href="https://www.sealights.io/" target="_blank">SeaLights</a>, an Israeli company that offers a management platform for Continuous Testing, asked me to come on the webinar and share my views on test automation and continuous testing. In this post, I&#8217;ll share some of the thoughts and opinions I talked about there.

**Test automation is everywhere, however&#8230;**  
Test automation is everywhere. That&#8217;s probably nothing new.

A lot of organizations are adopting Continuous Integration and Continuous Delivery. Also nothing new.

To be able to &#8216;do&#8217; CI/CD, a lot of organizations are relying on their automated tests to help safeguard quality thresholds while increasing release speed. Again, no breaking news here.

However, to safeguard quality in CI and CD you&#8217;ll need to be able to do Continuous Testing (CT). Here&#8217;s my definition of CT, which I used in the webinar:

> Continuous Testing is a process that allows you to gauge the quality of your software on demand. No matter if you&#8217;re building and deploying once a month or once a minute, CT allows you to get insight into application quality at all times.

It won&#8217;t come as a surprise to you that automated tests often form a big part of an organization&#8217;s CT strategy. However, just having automated tests is not enough to be able to support CT. Your automated test approach should not just cover application functionality and coverage, but also:

  * A solid test data management strategy
  * Effective test environment management
  * Informative reporting, targeted towards all relevant audiences

And probably many more things that I&#8217;m forgetting here..

In order to be able to leverage your automated tests successfully for supporting CT, I&#8217;ve come up with a model based on four pillars that need to be in place:

<a href="http://www.ontestautomation.com/?attachment_id=1871" rel="attachment wp-att-1871"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/05/at_ct_fitr.png" alt="From AT to CT with FITR tests" width="1135" height="673" class="aligncenter size-full wp-image-1871" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/05/at_ct_fitr.png 1135w, https://www.ontestautomation.com/wp-content/uploads/2017/05/at_ct_fitr-300x178.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/05/at_ct_fitr-768x455.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/05/at_ct_fitr-1024x607.png 1024w" sizes="(max-width: 1135px) 100vw, 1135px" /></a>

Let&#8217;s take a quick look at each of these FITR pillars and how they are necessary in CT.

**Focused**  
Automated tests need to be focused to effectively support CT. &#8216;Focused&#8217; has two dimensions here.

First of all, your tests should be targeted at the right application component and/or layer. It does not make sense to use a user interface-driven test to test application logic that&#8217;s exposed through an API (and subsequently presented through the user interface), for example. Similarly, it does not make sense to write API-level tests that validate the inner workings of a calculation algorithm if unit tests can provide the same level of coverage. By now, most of you will be familiar with the <a href="https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid" target="_blank">test automation pyramid</a>. While I think it&#8217;s not to be used as a guideline, I find that the pyramid does provide a good starting point for discussion when it comes to focusing your tests at the right level. Use the model to your advantage.

The second aspect of focused automated tests is that your tests should test what they can do effectively. This boils down to sticking to what your test solution and tools in it do best, and leaving the rest either to other tools or to testers, depending on what&#8217;s there to be tested. Don&#8217;t try and force your tool to do things it isn&#8217;t supposed to (<a href="http://www.ontestautomation.com/how-not-to-test-restful-apis-with-selenium-webdriver/" target="_blank">here&#8217;s an example</a>).

If your tests are unfocused, they are far more likely to be slow to run, to have high maintenance costs and to provide inaccurate or shallow feedback on application quality.

**Informative**  
Touching upon shallow or inaccurate feedback, automated tests also need to be informative to effectively support CT. &#8216;Informative&#8217; also has two separate dimensions.

Most importantly, the results produced and the feedback provided by your automated tests should allow you, or the system that&#8217;s doing the interpretation for you (such as an automated build tool), make important decisions based on that feedback. Make sure that the test results and reporting provided contain clear results, information and error messages, targeted towards the intended audience. Keep in mind that every audience has its own requirements when it comes to this information. Developers likely want to see stack trace, whereas managers don&#8217;t. Find out what the target audience for your reporting and test results is, what their requirements are, and then cater to them as best as you can. This might mean creating more that one report (or source of information in general) for a single test run. That&#8217;s OK.

Another important aspect of informative automated tests is that it should be clear what they do (and what they don&#8217;t do). You can make your tests themselves be more informative through various means, including (but not limited to) using naming conventions, using a BDD tool such as Cucumber or SpecFlow to create living documentation for your tests (blasphemy? maybe.. but if it works, it works), and following good programming practices to make your code better readable and maintainable.

When automated test solutions and the results they produce are not informative, valuable time is wasted analyzing shallow feedback, or gathering missing information, which evidently breaks the &#8216;continuous&#8217; part of CT.

**Trustworthy**  
When you&#8217;re relying on your automated tests to make important decisions in your CT activities, you&#8217;d better make sure they&#8217;re trustworthy. As I described in more detail in <a href="http://www.ontestautomation.com/trust-automation/" target="_blank">previous</a> <a href="http://www.ontestautomation.com/on-false-negatives-and-false-positives/" target="_blank">posts</a>, automated tests that cannot be trusted are essentially worthless. Make sure to eliminate false positives (tests that fail when they shouldn&#8217;t), but also false negatives (tests that pass when they shouldn&#8217;t).

**Repeatable**  
The essential idea behind CT (referring to the definition I gave at the beginning of this blog post) is that you&#8217;re able to determine application quality on demand. Which means you should be able to run your automated tests on demand. Especially when you&#8217;re including API-level and end-to-end tests, this is often not as easy as it sounds. There are two main factors that can hinder the repeatability of your tests:

  * _Test data_. This is in my opinion one of the hardest ones to get right, especially when talking end-to-end tests. Lots of applications I see and work with have (overly) complex data models or share test data with other systems. And if you&#8217;re especially lucky, you&#8217;ll get both. A solid test data strategy should be put in place to do CT, meaning that you&#8217;ll either have to create fresh test data at the start of every test run or have the ability to restore test data before every test run. Unfortunately, both options can be quite time consuming (if at all attainable and manageable), drawing you further away from the &#8216;C&#8217; in CT instead of bringing you closer.
  * _Test environments_. If your application communicates with other components, applications or systems (and pretty much all of them do nowadays), you&#8217;ll need suitable test environments for each of these dependencies. This is also easier said than done. One possible way to deal with this is by using a form of simulation, such as mocking or <a href="http://www.ontestautomation.com/category/service-virtualization/" target="_blank">service virtualization</a>. Mocks or virtual assets are under your full control, allowing you to speed up your testing efforts, or even enable them in the first place. Use simulation carefully, though, since it&#8217;s yet another thing to be managed and maintained, and make sure to test against the real thing periodically for optimal results.

Having the above four pillars in place does not guarantee that you&#8217;ll be able to perform your testing as continuously as your CI/CD process requires, but it will likely give it a solid push in the right direction.

Also, the FITR model I described here is far from finished. If there&#8217;s anything I forgot or got wrong, feel free to comment or contact me through email. I&#8217;d love to get feedback.

Finally, if you&#8217;re interested in the webinar I talked about earlier, but haven&#8217;t seen it, it&#8217;s freely available on YouTube:

<span class="embed-youtube" style="text-align:center; display: block;"></span>