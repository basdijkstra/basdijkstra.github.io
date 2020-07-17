---
id: 1667
title: Managing test data in end-to-end test automation
date: 2016-11-18T10:15:10+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1664-revision-v1/
permalink: /1664-revision-v1/
---
One of the biggest challenges I&#8217;m facing in projects I&#8217;m contributing to is the proper handling of test data in automated tests, and especially in end-to-end test automation. For unit and integration testing, it is often a good idea to resort to mocking or stubbing the data layer to remain in control over the test data that is used in and required for the tests to be executed. When doing end-to-end tests, however, keeping all required test data in check in an automated manner is no easy task. I say &#8216;in an automated manner&#8217; here, because once you start to rely on manual intervention for the preparation or cleaning up of test data, then you&#8217;re moving away from the ability to test on demand, which is generally not a good thing. If you want your tests to truly run on demand, having to rely on someone (or a third party process) to manage the test data can be a serious bottleneck. Even more so with distributed applications, where teams often do not have enough control over the dependencies they require in order to be able to do end-to-end (or even integration) testing.

In this post, I&#8217;d like to consider a number of possible strategies for dealing with test data in end-to-end tests. I&#8217;ll take a look at their benefits and their drawbacks to see if there&#8217;s one strategy that trumps all others (spoiler alert: probably not&#8230;).

**Creating test data during test execution**  
One approach is to start every test, suite or run with a set-up phase where the test data required for that specific test, suite or run is created. This can be done by any technical means available: be it through direct INSERT statements in a database, a series of API calls that create new users, orders or any other type of test data object, or (if there really is no alternative) through the user interface. The main benefit of this approach is that there is a strong coupling between the created test data and the actual test, meaning that the right test data is always available. There&#8217;s some rather big drawbacks as well, though:

  * Setting up test data takes additional time, especially when doing through the user interface.
  * Setting up test data requires additional code, which increases the maintenance burden of your automated tests.
  * If an error occurs during the test data setup fase of your test, your actual test result will be unpredictable and therefore cannot be trusted. That is, if your test isn&#8217;t simply aborted before the actual test steps are executed at all&#8230;
  * This approach potentially requires tests to depend on one another in terms of the sequence in which they&#8217;re executed, which is a definite anti-pattern of test automation.

I&#8217;ve used this approach several times in my projects, with mixed results. Sometimes it works just fine, sometimes a little less so. The latter is most often the case when the data model is really complex and there&#8217;s no other way than mimicking user interaction by means of tools such as Selenium to get the data prepared.

**Query test data prior to test execution**  
The second approach to dealing with test data around automated tests is to query the data before the actual test runs. This can be done either directly on the database, or possibly through an API (or even a user interface) that allows you to retrieve customers, articles or whatever type of data object you need for your test. The main benefit of this approach is that you&#8217;re not losing time creating test data when all you really care about is the test results, and that this approach results in less test automation code to maintain, especially when you can query the database directly. Here too, there are a couple of drawbacks that render this approach less than ideal as well:

  * There&#8217;s no guarantee that the exact data you require for a test case (especially with edge cases) is actually present in the database. For example, how many customers that have lived in Nowhereville for 17,5 years, together with their wife and their blue parrot, are actually in your database?
  * Sometimes getting the query right so that you&#8217;re 100% sure that you get the right test data is a daunting task, requiring very specific knowledge of the system. This might make this approach less than ideal for some teams.
  * Also, even when you get the query exactly right, does that _really_ guarantee that you get results that you&#8217;re 100% sure will be a perfect fit for your test case?

**Reset the test data state before or after a test run**  
I think this is potentially the best approach when having to deal with test data in end-to-end tests: either setting the test data database to the exact state by restoring a database backup or cleaning up the test database afterwards. This guarantees that test data-wise, you&#8217;re always in the exact same state before / after a test run, which massively improves predictability and repeatability of your tests. The main drawback is that often, this is not an option either due to nobody knowing enough about the data model to allow this, or by access to the database being restricted for reasons good or less than good. Also, when you&#8217;re dealing with large databases, doing a database reset or rollback might be a matter of hours, which slows down your feedback loop significantly, rendering it next to useless when your tests are part of a CD pipeline.

**Virtualizing the data layer**  
Nowadays, there are several solutions on the market that allow you to effectively virtualize your data layer for testing purposes. A prime example of such a solution is <a href="https://www.delphix.com/solutions/test-data-management" target="_blank">Delphix</a>, but there are several other tools on the market as well. I haven&#8217;t experimented with any of these for long enough to actually have formed an educated opinion, but one thing I don&#8217;t really like about this approach is that virtualizing the data layer (however efficient it may be) voids the concept of executing a true end-to-end test, since there&#8217;s no actual data layer involved anymore. Then again, for other types of testing, it may actually be a very good concept, just like <a href="http://www.ontestautomation.com/category/service-virtualization/" target="_blank">service virtualization</a> is for simulating the behavior of critical yet hard-to-access dependencies in test environments.

**So, what&#8217;s your take on this?**  
In short, I haven&#8217;t found the ideal solution yet. I&#8217;d love to read about the approaches other people and teams are taking when it comes to managing test data in end-to-end automated tests, so feel free to send me an email, or even better, leave a comment to this post. Looking forward to seeing your replies!