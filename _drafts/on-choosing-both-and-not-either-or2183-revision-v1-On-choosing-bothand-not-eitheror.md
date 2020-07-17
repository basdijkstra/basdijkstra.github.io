---
id: 2185
title: On choosing both/and, not either/or
date: 2018-06-01T10:03:56+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/2183-revision-v1/
permalink: /2183-revision-v1/
---
Choices. We all make them tens of times each day. Peanut butter or cheese (cheese for me, most of the time). Jeans or slacks (jeans, definitely). Coffee or tea (decent coffee with a glass of water on the side please). And when you&#8217;re working on or learning about automation, there&#8217;s a multitude of choices you also can (and sometimes have to) make. A lot of these choices, as I see people discussing and making them, are flawed in my opinion, though. Some of them are even false dichotomies. Let&#8217;s take a look at the choices people think they need to make, and how there are other options available. Options that might lead to better results, and to being better at your job.

**Do I need to learn Java or .NET? Selenium or UFT?**  
Creating automation often involves writing code. So, the ability to write code is definitely a valuable one. However, getting hung up on a specific programming language might limit your options as you&#8217;re trying to get ahead.

I still see many people asking what programming language they need to learn when they&#8217;re starting out or advancing in their career. If you&#8217;d ask me, the answer is &#8216;it doesn&#8217;t really matter&#8217;. With the abundance in tools, languages, libraries and frameworks that are available to software development teams nowadays, chances are high that your next gig will require using a different language than your current one.

As an example, I recently started a new project. So far, in most of my projects I&#8217;ve written automation in either Java or .NET. Not in this one, though. In the couple of weeks I&#8217;ve been here, I&#8217;ve created automation using PHP, Go and JavaScript. And you know what? It wasn&#8217;t that hard. Why? Because I&#8217;ve made a habit of learning how to program and of studying principles of object oriented programming instead of learning the ins and outs of a specific programming language. Those specifics can be found everywhere on Google and StackOverflow.

The same goes for automation tools. I started writing UI-level automation using TestPartner. Then QuickTest Pro (now UFT). I&#8217;ve used Selenium in a few projects. I&#8217;ve dabbled with Cypress. Now, I&#8217;m using Codecept. It doesn&#8217;t matter. The principles behind these tools are much the same: you identify objects on a screen, then you interact with them. You need to take care of waiting strategies. If you become proficient in these strategies, which tool you&#8217;re using doesn&#8217;t matter that much anymore. I&#8217;ve stopped chasing the &#8216;tool du jour&#8217;, because there will always be a new one to learn. The principles have been the same for decades, though. What do you think would be a better strategy to improve yourself?

Identify and learn to apply common principles and patterns, don&#8217;t get hung up on a single tool or language. Choose both/and, not either/or.

**Do I stay a manual tester or become an automation engineer?**  
Another one of the choices I see people struggling with often is the one between staying a &#8216;manual tester&#8217; (a term that I prefer not to use for all the reasons Michael Bolton gives in <a href="http://www.developsense.com/blog/2017/11/the-end-of-manual-testing/" rel="noopener" target="_blank">this blog post of his</a> and becoming an automation engineer. If you&#8217;d ask me, this is a perfect example of a flawed choice in the testing field. It&#8217;s not a matter of either/or. It&#8217;s a matter of both/and.

Automation supports software testing, it does not replace it. If you want to become more proficient in automation, you need to become more proficient in testing, too. I&#8217;ve only fairly recently realized this myself, by the way. For years, all I did was automation, automation, automation, without thinking whether my efforts actually supported the testing that was being done. I&#8217;ve learned since that if you don&#8217;t know what testing looks like (hint: it&#8217;s much more than clicking buttons and following scripts), then you&#8217;ll have a pretty hard time effectively supporting those activities with automation.

Don&#8217;t abandon one type of role for the other one, especially when there&#8217;s so much overlap between them. Choose both/and, not either/or.

**Do I learn to write tests against the user interface, or can I better focus on APIs?**  
So, I&#8217;ve been writing a lot about the benefits of writing tests at the API level, not only on this blog, but also in numerous talks and training courses. When I do so, I am often quite critical about the way too many people apply user interface-driven automation. And there IS a lot of room for improvement there, definitely. That does not mean that I&#8217;m saying you should abandon this type of automation at all, just that you should be very careful when deciding where to apply it.

Like in the previous examples, it is not a matter of either/or. For example, consider something as simple and ubiquitous as a login screen (or any other type of form in an application). When deciding on the approach for writing tests for it, it&#8217;s not a simple choice between tests at the UI level or tests at the API level; rather it depends on what you&#8217;re testing. writing a test that checks whether an end user sees the login form and all associated in their browser? Whether the user can interact with the form? Whether the data entered by the user is sent to the associated API correctly? Or whether the form looks like it&#8217;s supposed to? Those are tests that should be carried out at the UI level. Checking whether the data provided by the user is processed correctly? Whether incorrectly formatted data is handled in the appropriate manner? Whether the right level of access grants is given to the user upon enter a specific combination of username and password? Those tests might target a level below the UI. Many thanks, by the way, to <a href="https://twitter.com/FriendlyTester/" rel="noopener" target="_blank">Richard Bradshaw</a> for mentioning this example somewhere on Slack. I owe you one more beer.

Being able to make the right decision on the level and scope to write the test on required knowing what the benefits and drawbacks and the possibilities of the alternatives are. It also requires the ability to recognize and apply principles and patterns to make the best possible decision.

Again, identify and learn to apply common principles and patterns, don&#8217;t get hung up on a single tool or language. Choose both/and, not either/or.

The point I&#8217;ve been trying to make with the examples above is that, like with so many things in life, being the best possible automation engineer isn&#8217;t a matter of choosing A over B. Of being able to do X or Y. What, in my opinion, will make you much better in your role is being able to do, or at least understand, A and B, X and Y. Then, extract their commonalities (these will often take the form of the previously mentioned principles and practices) and learn how to apply them. Study them. Learn more about them. Fail at applying them, and then learn from that.

I&#8217;m convinced that this is a much better approach to sustainable career development than running after the latest tool or hype and becoming a self-proclaimed expert at it, only to have to make a radical shift every couple of years (or even months, sometimes).

Don&#8217;t become a one trick pony. Choose both/and, not either/or.