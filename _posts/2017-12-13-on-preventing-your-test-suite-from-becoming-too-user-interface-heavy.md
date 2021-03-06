---
id: 2088
title: On preventing your test suite from becoming too user interface-heavy
date: 2017-12-13T12:16:50+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2088
permalink: /on-preventing-your-test-suite-from-becoming-too-user-interface-heavy/
categories:
  - API testing
  - General test automation
tags:
  - api testing
  - good test automation
  - user interface
---
In August of last year, I published a <a href="https://www.ontestautomation.com/do-you-want-a-framework-or-a-solution/" rel="noopener" target="_blank">blog post</a> talking about why I don&#8217;t like to think of automation in terms of frameworks, but rather in terms of solutions. I&#8217;ve softened a little since then (this is probably a sign of me getting old..), but my belief that building a framework _might_ lead to automation engineers subsequently trying to fit every test left, right and center into that framework still stands. One example of this phenomenon in particular I still see too often: engineers building a feature-rich end-to-end automation framework (for example using Selenium) and then automating all of their tests using that framework.

This is what I meant in the older post by &#8216;framework think&#8217;: _because_ the framework has made it so easy for them to add new tests, they skip the step where they decide what would be the most efficient approach for a specific test and blindly add it to the test suite run by that very framework. This might not lead to harmful side effects in the short term, but as the test suite grows, chances are high that it becomes unwieldy, that the time it takes to complete a full test run becomes unnecessarily long and that maintenance efforts are not being outweighed by the added value of having the automated tests any more.

In this post, I&#8217;d like to take the practical approach once more and demonstrate how you can take a closer look at your application and decide if there might be a more efficient way to implement certain checks. We&#8217;re going to do this by opening up the user interface and see what happens &#8216;under the hood&#8217;. I&#8217;m writing this post as an addendum to my &#8216;<a href="https://www.ontestautomation.com/training/building-great-end-to-end-tests-with-selenium-and-cucumber-specflow/" rel="noopener" target="_blank">Building great end-to-end tests with Selenium and Cucumber / SpecFlow</a>&#8216; course, by the way. Yes, that&#8217;s right, one of the first things I talk about during my course on writing tests with Selenium is when _not_ to do so. I firmly believe that&#8217;s the on of the very first steps towards creating a solid test suite: deciding what should not be in it.

**The application under test**  
The application we&#8217;re going to write tests for is an online mortgage orientation tool, provided by a major Dutch online bank. I&#8217;ve removed all references to the client name, just to be sure, but it&#8217;s not like we&#8217;re dealing with sensitive data here. The orientation tool is a sequence of three forms, in which people that are interested in a mortgage fill in details about their financial situation, after which the orientation tool gives an indication of whether or not the applicant is eligible for a mortgage, as well as an estimate of the maximum amount of the mortgage, the interest rate and the monthly installments payable.

<a href="http://www.ontestautomation.com/on-preventing-your-test-suite-from-becoming-too-user-interface-heavy/mortgage_orientation_tool_1/" rel="attachment wp-att-2092"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_1.png" alt="Our application under test - the mortgage orientation tool" width="1161" height="665" class="aligncenter size-full wp-image-2092" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_1.png 1161w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_1-300x172.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_1-768x440.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_1-1024x587.png 1024w" sizes="(max-width: 1161px) 100vw, 1161px" /></a>

**What are we going to automate?**  
Now that we know what our application under test does, let&#8217;s see what we should automate. We&#8217;ll assume that there is a justified need for automated checks in the first place (otherwise this would have been a very short blog post!). We&#8217;ll also assume that, maybe for tests on some other part of the bank&#8217;s website, there is already a solid automation framework written around Selenium in place. So, this being a website and all, it makes sense to write some additional checks and incorporate them into the existing framework.

First of all, let&#8217;s try and make sure that the orientation tool can be used and completed, and that it displays a result. I&#8217;d say, that would be a good candidate for an automated test written using Selenium, since it confirms that the application is working from an end user perspective (there is value in the test) and I can&#8217;t think of a lower level test that would give me the same feedback. Since there are a couple of different paths through the orientation tool (you can apply for a mortgage alone or with someone else, some people have a house to sell while others have not, there are different types of contracts, etc.), I&#8217;d even go as far as to say you&#8217;ll need more than one Selenium-based test to be able to properly claim that all paths can be traversed by an end user.

Next, I can imagine that you&#8217;d want to make sure that the numbers that are displayed are correct, so your customers aren&#8217;t misinformed when they complete the orientation tool. This would lead to some massive issues of distrust later on in the mortgage application process, I&#8217;d assume.. Since we&#8217;ve been able to add the previous tests so easily to our existing framework, it makes sense to add some more tests that walk through the forms, add the data required to trigger a specific expected outcome and verify that the result screen we saw in the screenshot above displays the expected numbers. Right?

No. Not right.

It&#8217;s highly likely that the business logic used to perform the calculation and serve the numbers displayed on screen isn&#8217;t actually implemented in the user interface. Rather, it&#8217;s probably served up by a backend service containing the business logic and rules required to perform the calculations (and with mortgages, there are quite a few of those business rules, I&#8217;ve been told..). The user interface takes the values entered by the end user, sends them to a backend service that performs calculations and returns the values indicating mortgage eligibility, interest rate, height of monthly installment, etc., which are then interpreted and displayed again by that same user interface.

So, since the business logic that we&#8217;re verifying isn&#8217;t implemented in the user interface, why use the UI to verify it in the first place? That would highly likely only lead to <a href="https://www.ontestautomation.com/is-your-user-interface-driven-test-automation-worth-the-effort/" rel="noopener" target="_blank">unnecessarily slow tests and shallow feedback</a>. Instead, let&#8217;s look if there&#8217;s a different hook we can use to write tests.

I tend to use on of two different tactics to find out if there are better ways to write automated tests in cases like these:

  1. Talk to a developer. They&#8217;re building the stuff, so they&#8217;ll probably know more about the architecture of your application and will likely be happy to help you out.
  2. Use a network analyzing tool such as <a href="https://www.telerik.com/download/fiddler" rel="noopener" target="_blank">Fiddler</a> or <a href="https://www.wireshark.org/" rel="noopener" target="_blank">WireShark</a>. Tools like these two let you see what happens &#8216;under water&#8217; when you&#8217;re using the user interface of a web application.

Normally, I&#8217;ll use a combination of both: find out more about the architecture of an application by talking to developers, then using a network analyzer (I prefer Fiddler myself) to see what API calls are triggered when I perform a certain action.

**Analyzing API calls using Fiddler**  
So, let&#8217;s put my assumption that there&#8217;s a better way to automate the tests that will verify the calculations performed by the mortgage orientation tool to the test. To do so, I&#8217;ll fire up Fiddler and have it monitor the traffic that&#8217;s being sent back and forth between my browser and the application server while I interact with the orientation tool. Here&#8217;s what that looks like:

<a href="http://www.ontestautomation.com/on-preventing-your-test-suite-from-becoming-too-user-interface-heavy/mortgage_orientation_tool_2/" rel="attachment wp-att-2090"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_2.png" alt="Traffic exchanged between client and server in our mortgage orientation tool" width="1920" height="909" class="aligncenter size-full wp-image-2090" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_2.png 1920w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_2-300x142.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_2-768x364.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/12/mortgage_orientation_tool_2-1024x485.png 1024w" sizes="(max-width: 1920px) 100vw, 1920px" /></a>

As you can see, there&#8217;s a mortgage orientation API with a _Calculate_ operation that returns exactly those numbers that appear on the screen. See the number I marked in yellow? It&#8217;s right there in the application screenshot I showed previously. This shows that pretty much all that the front end does is performing calls to a backend API and presenting the data returned by it in a manner attractive to the end user. This means that it would not make sense to use the UI to verify the calculations. Instead, I&#8217;d advise you to mimic the API call (or sequence of calls) instead, as this will give you both faster and more accurate feedback.

To take things even further, I&#8217;d recommend you to dive into the application even deeper and see if the calculations can be covered with a decent set of unit tests. The easiest way to do this is to start talking to a developer and see if this is a possibility, and if they haven&#8217;t already done so. No need to maintain two different sets of automated checks that cover the same logic, and no need to cover logic that can be tested through unit tests with API-level checks..

Often, though, I find that writing tests like this at the API level hits the sweet spot between coverage, effort it takes to write the tests and speed of execution (and as a result, length of the feedback loop). This might be because I&#8217;m not too well versed in writing unit tests myself, but it has worked pretty well for me so far.

**Deciding what to automate where: a heuristic**  
The above has just been one example where it would be better (as well as easier) to move specific checks from the UI level to the API level. But can we make some more generic statements about when to use UI-level checks and when to dive deeper?

Yes, we can. And it turns out, someone already did! In a recent blog post called &#8216;<a href="http://chrismcmahonsblog.blogspot.nl/2017/11/ui-test-heuristic-dont-repeat-your-paths.html" rel="noopener" target="_blank">UI Test Heuristic: Don&#8217;t Repeat Your Paths</a>&#8216;, Chris McMahon talked about this exact subject, and the heuristic he presents in his blog post applies here perfectly:

  * Check that the end user can complete the mortgage orientation tools and is shown an indication of mortgage eligibility and associated figures > different paths through the user interface > user interface-level tests
  * Check that the figures served up by the mortgage orientation tool are correct > repeating the same paths multiple times, but with different sets of input data and expected output values > time to dive deeper

So, if you want to prevent your automated test suite from becoming too bloated with UI tests, this is a rule of thumb you can (and frankly, should) apply. As always, I&#8217;d love to hear what you think.