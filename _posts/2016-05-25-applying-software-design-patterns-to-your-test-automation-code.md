---
id: 1450
title: Applying software design patterns to your test automation code
date: 2016-05-25T08:00:12+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1450
permalink: /applying-software-design-patterns-to-your-test-automation-code/
categories:
  - General test automation
tags:
  - development
  - patterns
  - test automation
---
Yes, yes, yes... The creation of automated checks as a means of supporting and speeding up parts of the software development and testing process (a concept better known as 'test automation') <a href="http://www.kohl.ca/2005/test-automation-is-software-development/" target="_blank">should be considered software development</a>. I&#8217;ve mentioned that a couple of times on this blog already, and today I&#8217;m doing it again. In this post, I&#8217;d like to take a look at why it&#8217;s probably a good idea to apply good software design patterns to your test automation code. Before I ramble on, please take note that I deliberately don&#8217;t call these patterns &#8216;best practices&#8217;. This is because sticking the &#8216;best practice&#8217; label onto a software development pattern or habit usually promises something that that pattern cannot live up to. For each &#8216;best practice&#8217;, there&#8217;s at least one situation (but usually a lot more) where this practice just isn&#8217;t the best one to be applied. So, instead of &#8216;best practice&#8217; it should be called &#8216;best practice for situations X and Y, but not for situation Z&#8217;. Or &#8216;best practice, but only on a Tuesday&#8217;. Instead, I think it&#8217;s best just to steer clear of the term altogether.

With that personal gripe out of the way, let's look at why I think you should make use of proven software design and implementation patterns to improve your test automation code. Basically, it comes down to three aspects: maintainability, maintainability and .. maintainability. Applying patterns will not improve the quality and effectiveness of your checks, but it will do wonders for what probably is the most time-consuming task in test automation: maintaining the test code to ensure that it stays fresh, up to date and, well, running. Code that does not follow any basic software design patterns is likely to require much more effort to be read, understood and maintained. This is especially the case when you&#8217;re the lucky guy or girl who&#8217;s assigned the task of maintaining test code that you didn&#8217;t write yourself, but I definitely wouldn&#8217;t rule out the chance that you return to your own badly written test code after a couple of weeks and start thinking &#8216;what the &#8230; was THAT supposed to do again?&#8217;.

So, in short, you should apply good design and implementation patterns and practices. Here are some of the most common ones.

**Don't Repeat Yourself (DRY)**  
Being the opposite of WET (Write Everything Twice), DRY states that &#8216;Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.&#8217; Or, as I like to explain DRY with regards to test automation, if any change to your application under test requires you to make the same change twice in your test code, you&#8217;re doing it wrong. Any object on any level in your test code (from a single constant all the way up to a sequence of actions required to simulate a certain business process) should be defined exactly once. Some test automation-related examples of the DRY pattern in action are:

  * The use of constants classes for anything from default timeout values to database connection strings &#8211; update once, effective everywhere
  * The Page Object pattern in Selenium &#8211; all elements, actions and checks related to a certain page or part of a page are defined in a central spot: the Page Object
  * The use of Examples in your Cucumber specs &#8211; A scenario is written once and then repeated multiple times with different input and validation values

![dry](/images/blog/keep-calm-and-don-t-repeat-yourself.png "Don't Repeat Yourself")

**Keep It Simple, Stupid (KISS)**  
While the DRY principle is probably the best known in software development, I think the KISS principle is just as important and just as (if not more) effective when it comes to maintainability of your test automation code. In my own words, this principle states that the solution that is easiest to implement is almost always the best one. Therefore, no matter what the problem is, you should always strive for the simplest solution possible. Some examples with regards to test automation:

  * Use predefined libraries instead of writing your own solutions. I&#8217;ve been guilty of this myself as well. For example, I still receive comments on <a href="http://www.ontestautomation.com/create-your-own-html-report-from-selenium-tests/" target="_blank">this post</a> regarding creating your own HTML reports for Selenium tests, even though I&#8217;ve since discovered and written multiple times about <a href="http://www.ontestautomation.com/creating-html-reports-for-your-selenium-tests-using-extentreports/" target="_blank">ExtentReports</a>, a predefined library that does everything you want and more. No maintenance needed, just import it and you&#8217;re ready to go. This saves you a lot of unnecessary code to maintain as well.
  * Don&#8217;t automate everything in sight. Automating something just because you can or because your manager or (even worse) a senior test automation engineer (who should know better) is a recipe for disaster. I&#8217;m currently writing another blog post (one that will probably end up on LinkedIn Pulse rather than this blog) on why you should first ask the &#8216;why&#8217;, then the &#8216;what&#8217;, and then finally the &#8216;how&#8217;. This will save you from writing and maintaining a lot of useless checks, which in turn makes your solution a lot simpler.

![kiss](/images/blog/keepitsimplestupid.png "Keep It Simple, Stupid")

**Choose a proper naming convention and stick to it**  
The last pattern that I think should definitely be applied to test automation code &#8211; or even better: to all related artifacts &#8211; is the use of a proper <a href="https://en.wikipedia.org/wiki/Naming_convention_(programming)" target="_blank">naming convention</a>. I don&#8217;t really have a preference to a specific naming convention (although it took me a while to get used to C# methods starting with an uppercase character with me coming from Java), but picking one and sticking with it has definite benefits:

  * Assuming you&#8217;ve picked a naming convention that requires you to use descriptive names for your variables and methods (forget any convention that does not do this), using one and the naming convention everywhere keeps your code readable to anyone involved. By this, maintainability is also improved since it should be clear from the names used what a specific variable or method is actually representing or doing.
  * Using proper naming (and structuring) conventions for files and folders allows anybody on your team to locate that one pesky file or test automation report that&#8217;s being requested.

![naming_convention](/images/blog/naming_convention.gif "A Dilbert comic joking about naming conventions")

**Start from the get go**  
To ensure that your test automation code remains readable and maintainable, you should apply good software design patterns from the very first line of code onward. Even if you&#8217;re just writing a little script to set up some test data or to perform a short and quick end user routine, you never know what those five lines of code will grow into one day. With code, it&#8217;s a bit like with pavements: it&#8217;s easy to leave behind rubbish when someone before you has done the same. However, when the pavement (or the code) is spotless, you&#8217;ll probably feel bad about leaving behind any trash after you&#8217;ve paid a visit. Always keep in mind that refactoring and improving badly written code is something no developer likes to do.

**Be pragmatic**  
Of course, as with so many other things in life, applying patterns isn&#8217;t a case of black or white. It might not make sense to apply patterns to throw-away code (code that&#8217;s only used once). Also, with regards to the DRY principle, there&#8217;s such a thing as <a href="http://www.justinweiss.com/articles/i-dry-ed-up-my-code-and-now-its-hard-to-work-with-what-happened/" target="_blank">too DRY code</a>. In general, though, applying good software design patterns will definitely benefit your code and those who rely on or need to work with it. As I said at the beginning of this post, test automation is software development. Let&#8217;s start treating it as such.