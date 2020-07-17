---
id: 1558
title: 'Continuous Delivery and user interface-driven test automation: does that compute?'
date: 2016-08-03T20:24:38+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1555-revision-v1/
permalink: /1555-revision-v1/
---
In this post, I&#8217;d like to take a closer look at the combination of Continuous Delivery on the one hand and automated tests at the user interface level on the other hand. Is this a match made in heaven? In hell? Or is the truth somewhere out there in between? (Hint: as with so many things in life, it is..).

Continuous Delivery (CD) is an approach in which software development teams produce software and release it into the production environment in very short cycles. Automation of building, testing and deploying the software often is a vital part in achieving CD. Since this is a blog on testing and test automation, I&#8217;ll focus on that, leaving the topics of build and deployment automation to those more experienced in that field.

Automated tests on the user interface level (such as those built using Selenium) traverse your complete application from the user interface to the database and back and can therefore be considered as end-to-end tests. These tests are often:

  * relatively slow to execute, since they require firing up an actual browser instance, rendering pages, dealing with page loading times, etc.,
  * demanding in terms of maintenance, since the user interface is among the components of an application that are most prone to change during the software life cycle, and
  * brittle, because object on a web page or in an application are often dynamically generated and rendered and because wait times are not always predictable, making synchronization a tough issue to tackle correctly.

So, we have CD striving for fast and reliable test feedback (CD is hard to do properly when you&#8217;ve got flaky tests stalling your builds) one the one hand, and user interface tests and their issues on the other hand. So, is there a place for these tests in the CD pipeline? I&#8217;d like to argue there is, if they satisfy a number of criteria.

**The tests are actually verifying the user interface**  
There&#8217;s a difference between verifying the user interface itself, and using the user interface to verify (business) logic implemented in lower layers of your application. If you&#8217;re merely using the user interface as a means to verify, for example, API or database logic, you should reconsider moving the test to that specific level. The <a href="http://martinfowler.com/bliki/TestPyramid.html" target="_blank">test automation pyramid</a> isn&#8217;t popular without reason.. On the other hand, if it IS the user interface you&#8217;re testing, then you&#8217;re on the right track. But maybe there is a better option&#8230;

**The user interface cannot be tested as a unit**  
Instead of verifying your user interface by running end-to-end tests, it might be worthwhile to see whether you can isolate the user interface in some way and test its logic as a unit instead. If this is possible, it will likely result in significant gains in terms of time needed to execute tests. I&#8217;ve recently written <a href="http://www.ontestautomation.com/an-approach-to-test-your-user-interface-more-efficiently/" target="_blank">a blog post</a> about this, so you might want to check that one out too.

**The tests are verifying vital application logic**  
This point is more about the &#8216;what&#8217; of the tests than the &#8216;how&#8217;. If you want to include end-to-end user interface-driven tests in your CD pipeline, they should verify business critical application features or logic. In other words, ask yourself &#8216;if this test fails, do we need to stop deploying into production?&#8217; If the answer is yes, then the test has earned its place in the pipeline. If not, then maybe you should consider taking it out and running it periodically outside of the pipeline (no-one says that all tests need to be in the pipeline or that no testing can take place outside of the pipeline!). or maybe removing the test from your test set altogether, if it doesn&#8217;t provide enough value.

**The tests are optimized in terms of speed and reliability**  
Once it&#8217;s clear that your user interface-driven end-to-end tests are worthy of being part of the CD pipeline, you should make sure that they&#8217;re as fast and stable as possible to prevent unnecessarily long delivery times and false negatives (and therefore blocked pipelines) due to flaky tests. For speed, you can for example make sure that there are no superfluous waits in your tests (_Thread.sleep()_, anyone?), and in case you have a lot of tests to execute &#8211; and all these tests should be run in the pipeline &#8211; you can see if it&#8217;s possible to parallelize test execution and have them run on different machines. For reliability, you should make sure that your error handling is top notch. For example, you should avoid any _StaleElementReferenceException_ occurrence in Selenium, something you can achieve by <a href="http://www.ontestautomation.com/using-wrapper-methods-for-better-error-handling-in-selenium/" target="_blank">implementing proper wrapper methods</a>.

In short, I&#8217;d say you should free up a place for user-interface driven end-to-end tests in your CD pipeline, but it should be a very well earned place indeed.