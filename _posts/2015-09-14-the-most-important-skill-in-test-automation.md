---
id: 1054
title: The most important skill in test automation
date: 2015-09-14T11:50:04+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1054
permalink: /the-most-important-skill-in-test-automation/
categories:
  - General test automation
tags:
  - alan page
  - skill
  - test automation
---
On LinkedIn, as well as on various test-oriented discussion boards there&#8217;s a lot of talk about the necessary skills one needs to have or develop in order to be a good test automation engineer / consultant / _<< insert job title here>>_. Example questions asked can be &#8216;<a href="http://testhuddle.com/forums/topic/what-languages-do-you-need-for-testing/" target="_blank">What language is useful to learn?</a>&#8216; or &#8216;How do I automate fancy technology XYZ using tool this-or-that?&#8217; (too many hits on LinkedIn to count, often related to Selenium).

Another perspective on the discussion on skills needed for successful test automation is whether you&#8217;re best off being &#8211; or employing &#8211; a developer with a testing mindset or a tester with development skills (I&#8217;d go with the latter).

However, in all those discussions, one vital, even essential skill everybody in test automation should have seems to be overlooked again and again:

**Knowing what not to automate.**

Honestly, some of the questions that pop up.. &#8216;How do I check the values on this pie chart with this tool?&#8217; &#8216;How can I have my test script read an email using Outlook?&#8217; Seriously? Why not just do a quick check on the underlying values (in a database, for example) instead of spending days developing a buggy test script that performs the same check, only slower?

In the wise words of <a href="http://leanpub.com/TheAWord" target="_blank">Alan Page</a>: &#8220;<a href="http://blog.fogcreek.com/the-abuse-and-misuse-of-test-automation-interview-with-alan-page/" target="_blank">You should automate 100% of the tests that should be automated</a>&#8220;. No more, and if possible no less either. But, when in doubt, it&#8217;s probably better not to automate a test that should be automated than to automate a test that shouldn&#8217;t be.

Still thinking about automating that Google Maps / Google Docs user interface check? Then please also consider the following likely outcomes:

  * You&#8217;ll probably introduce a lot of required maintenance effort (the Google UI&#8217;s change often and are notoriously hard to automate)
  * You&#8217;re not testing your actual product (unless you work for Google, and in that case, you probably know about their APIs which are much easier and faster to automate)
  * You&#8217;re throwing away money.

Please, don&#8217;t become the world&#8217;s best automator of useless checks.