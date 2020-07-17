---
id: 1537
title: Taking care of your test automation health
date: 2016-07-27T08:00:03+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1537
permalink: /taking-care-of-your-test-automation-health/
categories:
  - General test automation
tags:
  - code improvement
  - test automation health
---
_This blog post is heavily inspired by <a href="http://www.grove.co.uk/" target="_blank">Mark Fewster</a>&#8216;s talk at the 2016 Test Automation Day. I believe that the topic of test automation health is an important yet often overlooked subject, so I decided to dedicate a blog post to it. Many of the concepts have been borrowed from his talk, so Mark, in the unlikely case you&#8217;re reading this, thanks! The images included in this blog post are pictures I took from some of his slides at TAD as well._

So, some time ago, you decided to create (or were given the task of creating) a test automation solution to support your testing activities. You started designing, building and testing your solution (you did all that, right?), you implemented some tests and then demonstrated the solution to your team and to management. Cue thunderous applause. That was some time ago, and thinking back you realize that it pretty much all went downhill from there. Even though you added a lot more tests, increasing your test coverage and approaching the magic / ridiculous (strike out what does not apply) 100%, trust in your solution has dwindled. Tests are starting to get flaky, maintenance has become an ever increasing burden and you even start overhearing some people discussing ignoring the automated test results altogether &#8216;because frankly, I don&#8217;t trust them&#8217;.. What went wrong? And what could you have done to prevent this from happening?

Chances are that you got carried away adding more and more tests to your solution, ignoring a vital part of test automation development: the health of your solution itself. It&#8217;s a bit like becoming a decent distance runner: you can&#8217;t do that simply by running a little further every day. If you use that approach, you&#8217;ll soon get burnt out and feel tired permanently. It&#8217;s better to take a rest day every now and then, get a sports massage and buy a new pair of shoes when the old ones are no longer performing well.

The same goes for building upon your initial test automation solution: if you don&#8217;t take regular stock of how your solution is performing and take the time to do the necessary updates and alterations to make it run smoothly again, the risks of it getting tired and performing suboptimal will increase by the day.

<a href="http://www.ontestautomation.com/taking-care-of-your-test-automation-health/test_automation_metrics/" rel="attachment wp-att-1539"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/test_automation_metrics.jpg" alt="Discern between test automation progress and test automation health" width="2016" height="1512" class="aligncenter size-full wp-image-1539" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/test_automation_metrics.jpg 2016w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_automation_metrics-300x225.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_automation_metrics-768x576.jpg 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_automation_metrics-1024x768.jpg 1024w" sizes="(max-width: 2016px) 100vw, 2016px" /></a>

But what causes this phenomenon in the first place? I can think of a couple of reasons..

**Production code and test automation code are not treated equally**  
Let&#8217;s make some assumptions:

  * You want your production code to run in a stable, predictable and well performing manner.
  * You want to be able to roll back to a previous version of your production code in case something bad happens.
  * You want your production code to be clean, conforming to code standards and well maintainable.

Nothing out of the ordinary or too demanding, no? What if we replace &#8216;production code&#8217; with &#8216;test automation code&#8217; in the list above? Do the assumptions still hold? If not, there&#8217;s work to be done. Boldly put: **your test automation code is just as important as your production code**. You and your organizarion rely on your test automation code to inform you about the quality of your production code. Go / no-go decisions for deployment into production are made (partly or wholly) based on the outcome of the checks defined in your test automation code. Shouldn&#8217;t this code be treated at least as carefully as your production code, then?

<a href="http://www.ontestautomation.com/taking-care-of-your-test-automation-health/test_goals_vs_test_automation_goals/" rel="attachment wp-att-1540"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/test_goals_vs_test_automation_goals.jpg" alt="Testing goals vs. test automation goals" width="2016" height="1512" class="aligncenter size-full wp-image-1540" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/test_goals_vs_test_automation_goals.jpg 2016w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_goals_vs_test_automation_goals-300x225.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_goals_vs_test_automation_goals-768x576.jpg 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/test_goals_vs_test_automation_goals-1024x768.jpg 1024w" sizes="(max-width: 2016px) 100vw, 2016px" /></a>

**The objectives for test automation are geared towards testing, not to performance and maintenance**  
Test automation is not a goal in itself, but a means to the goal of giving insight in the quality of production code (also known as &#8216;testing&#8217;). However, this does not mean that settings goals specific to test automation can simply be forgotten as an activity. A lot of goals set by a development team (or management, if you&#8217;re still in an old-fashioned organization) are geared towards testing, not test automation. For example, you&#8217;ll often see goals such as:

  * At least X % of our test cases (who uses those anymore?) should be automated
  * Our test cycle time should be shortened with Y % if we implement test automation

Not only can you argue about the value of these goals themselves, but there&#8217;s something else at hand too: these goals are geared towards testing, not specific to test automation. I rarely (if at all) see goals such as:

  * Our automated tests should report less than X % false negatives / false positives per run / per month
  * The time it takes to implement automated tests should become X % shorter over time as our solution matures

I think these metrics are just as valuable as the ones I previously mentioned. To be honest, I think the first one is the most valuable of all.. I&#8217;d rather have no automated test than a test I do not trust.

**Effects of not taking care of test automation health**  
The points above to me are reason enough to take good care of my test automation health. And I think it should be good enough reason for you, too. But what would happen if you don&#8217;t? Test automation code that&#8217;s not properly taken care of will cause:

  * An increased risk of flaky tests whose outcome cannot be trusted, which leads to
  * distrust in test automation results from your team and other stakeholders, which leads to
  * risk of abandonment of the test automation solution altogether (shelfware), which leads to
  * tests being executed by hand again (either in parallel with the poorly performing automated tests or instead of that), which ultimately leads to
  * an increase in time required to perform the necessary tests, instead of the decrease desired when test automation was introduced

<a href="http://www.ontestautomation.com/taking-care-of-your-test-automation-health/monitoring/" rel="attachment wp-att-1541"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/07/monitoring.jpg" alt="Monitoring test automation health" width="2016" height="1512" class="aligncenter size-full wp-image-1541" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/07/monitoring.jpg 2016w, https://www.ontestautomation.com/wp-content/uploads/2016/07/monitoring-300x225.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/07/monitoring-768x576.jpg 768w, https://www.ontestautomation.com/wp-content/uploads/2016/07/monitoring-1024x768.jpg 1024w" sizes="(max-width: 2016px) 100vw, 2016px" /></a>

So, to wrap things up, it might be a good idea to start taking care of your test automation health. Start measuring both the quality of your test automation code (there&#8217;s lots of great tools for that) and the results they provide, for example in terms of false positives and false negatives as well as mean time required to add new checks. Make test automation health an objective in itself, and make someone (a team, or if need be a person) responsible for it. Measure, display and try to improve continually. Your test automation code will thank you for it.