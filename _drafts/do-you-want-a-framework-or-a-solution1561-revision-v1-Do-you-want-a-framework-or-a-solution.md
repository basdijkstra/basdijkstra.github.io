---
id: 1566
title: Do you want a framework or a solution?
date: 2016-08-10T20:39:31+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1561-revision-v1/
permalink: /1561-revision-v1/
---
Reading through recent comments on this blog, as well as questions that are being asked in some of the LinkedIn groups I am a member of, something has started to strike me as odd. It seems like that in every other comment, someone is asking &#8216;how do I add so and so to my automation framework?&#8217; or &#8216;how do I design a framework that does X, Y and Z?&#8217;. It isn&#8217;t the question as such that is the problem (if you can call me thinking of something as odd a problem in the first place), questions are good. You learn a lot by asking questions, and even more by asking the right questions. No, what gets to me is that all of these questions refer to automation frameworks. Why does it seem that so many people are so focused on &#8216;designing&#8217; a &#8216;framework&#8217;? In this post, I&#8217;d like to explain why I think it&#8217;s better not to focus on frameworks in test automation, but shift your attention to something much more important.

First of all, what is a framework anyway? If you&#8217;d ask me, this is a framework:

<a href="http://www.ontestautomation.com/?attachment_id=1562" rel="attachment wp-att-1562"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/08/framework.jpg" alt="Now this is what I&#039;d call a framework" width="698" height="400" class="aligncenter size-full wp-image-1562" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/08/framework.jpg 698w, https://www.ontestautomation.com/wp-content/uploads/2016/08/framework-300x172.jpg 300w" sizes="(max-width: 698px) 100vw, 698px" /></a>

But within the test automation realm, what does a framework constitute? Wikipedia defines a <a href="https://en.wikipedia.org/wiki/Test_automation#Framework_approach_in_automation" target="_blank">test automation framework</a> as:

> A test automation framework is an integrated system that sets the rules of automation of a specific product. This system integrates the function libraries, test data sources, object details and various reusable modules. These components act as small building blocks which need to be assembled to represent a business process. The framework provides the basis of test automation and simplifies the automation effort.

So, it&#8217;s an integrated system of libraries, modules and other stuff that sets the rules of automation. And there&#8217;s exactly the main gripe I have with talking and thinking about automation in terms of frameworks. Your framework shouldn&#8217;t be the one setting the rules for automation. Instead, your application under test, the quality of that application you want to have achieved when deploying it into production and the tests and checks that form the evidence of this quality should be the ones setting the rules. Test automation is merely one of the possible means to adhere to these rules.

When you start your test automation efforts by building a framework, either from scratch or by glueing together two or more existing tools and only then try and fit in all tests you want to perform, you&#8217;re approaching the problem from the wrong end. Trying to fit everything into an existing framework limits flexibility, as your prime concern becomes &#8216;how can I perform this test using my framework?&#8217; instead of &#8216;what is the best way to execute this test?&#8217;. It&#8217;s a bit like buying a two-bedroom apartment and only then realizing you&#8217;ve got six children. And a horse. Sure, it might fit, but it&#8217;s not necessarily going to be comfortable. It&#8217;s the same with test automation. Building a framework (the two-bedroom apartment) and then trying to fit in all sorts of tests (your six children, plus yourself and your significant other) will probably not lead to the most efficient (comfortable) situation. And don&#8217;t get me started on that horse..

Consider the following scenario: you have created a number of automated tests for your web application using Selenium (for browser automation), Cucumber (for BDD support) and a tool such as TestNG or JUnit (for assertions and possibly for reporting). At some point in time, you&#8217;re asked to also start writing automated tests for the newly built API that exposes part of the application logic. What do you do?

  1. Do you write some more tests that exercise the user interface, which in turn invoke the API &#8216;under the hood&#8217;?
  2. Do you search for an API testing tool that fits best inside your framework, glue it all together, start specifying features and scenarios for the API tests and automate away?
  3. Or do you look for the tool that best supports your actual API testing needs, use that and make it part of your overall testing activities, for example by making API tests a stage in your Continuous Integration or Continuous Delivery process?

If you chose #1, then I have three words for you (well, technically, four): **don&#8217;t do that**. If you opted for #2, you might have thought that API testing capabilities would be a nice addition to your framework, making it more versatile than it was before. But are you sure you can specify the behavior of your API in the same manner (i.e., describing the business value with the same amount of clarity) in Given/When/Then format? What if you discarded an API testing tool that fits the task at hand much better, just because it didn&#8217;t fit in your framework?

Option #1 and especially #2 are examples of what I&#8217;d like to call &#8216;_framework think_&#8216;. While you think you&#8217;re building something flexible and reusable, you´re really limiting yourself by discarding solutions that do not fit the framework. Option #3, on the other hand, describes what I&#8217;d call &#8216;_solution think_&#8216;, in my opinion a far better way of thinking and talking about test automation.

Test automation efforts should serve a higher purpose: solving the problem of giving fast and reliable insight into specific aspects of application quality. Every decision that&#8217;s made when implementing test automation, from the decision whether or not to automate at all down to selecting a specific tool to perform a certain test, should be made with this problem in mind. You should always ask: does this decision move me closer to providing a solution? And to what problem? The second question is equally, if not more important, since the perfect solution to a problem that&#8217;s irrelevant is just as wasteful as a solution that&#8217;s not right in the first place.

<a href="http://www.ontestautomation.com/?attachment_id=1563" rel="attachment wp-att-1563"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/08/solution.gif" alt="Stop framework think, start solution think" width="620" height="285" class="aligncenter size-full wp-image-1563" /></a>

So, in short, here&#8217;s my advice: stop &#8216;framework think&#8217;. Instead, start practicing &#8216;solution think&#8217;. It might be only a change in mindset, but sometimes that&#8217;s all it takes to get you in the right direction.

Thanks to <a href="http://www.eviltester.com/" target="_blank">Alan Richardson</a> for putting me on track to write this post with a quote from his 2015 Test Automation Day keynote. He also wrote a <a href="http://seleniumsimplified.com/2016/08/question-what-is-the-best-page-object-framework-for-java/" target="_blank">somewhat related post</a> recently, which I think you should all read.