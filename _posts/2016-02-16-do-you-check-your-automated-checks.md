---
id: 1254
title: Do you check your automated checks?
date: 2016-02-16T11:42:19+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1254
permalink: /do-you-check-your-automated-checks/
categories:
  - General test automation
tags:
  - mutation testing
  - quality
  - test automation
  - test automation pyramid
---
> Quis custodiet ipsos custodes? (Who will guard the guards?) 

&#8211; Juvenal (Roman poet), from Satires (Satire VI, lines 347–8)

You&#8217;ve been coding away on your automated tests for weeks or even months on end, creating a test suite that you think is up there with the best of them. Worthy of a place in the test automation Champions League, should such a competition exist. But is your work really that good? While you may think it is, do you have the proof to back it up? In this post, I would like to explain why you should make a habit of testing your tests, or, in light of the <a href="http://www.satisfice.com/blog/archives/856" target="_blank">checking vs. testing</a> debate, of checking your checks.

**Why would you want to check your checks?**  
To argument the need for checking your checks, let&#8217;s take a look at what might happen when you don&#8217;t do so regularly. Essentially, there are two things that can (and will) occur:

_1. Your checks will go bad_  
Checks that are left unattended once created, might &#8211; either right from the get-go or over time as the application under test evolves &#8211; start to:

  * **Fail consistently**. This will probably be detected and fixed as soon as your build monitor will start turning red. When you do Continuous Delivery or Continuous Deployment, this is even more likely as consistently failing checks will stall the delivery process. And people will start to notice that!
  * **Fail intermittently**. Also known as &#8216;flakiness&#8217;. Incredibly annoying as it&#8217;s usually a lot harder to pinpoint the root cause compared to checks that fail all the time. Still, the value of flaky checks can be considered about the same as for consistently failing checks: zero.
  * **Always return true**. This one might turn out to be very hard to detect, because your runs will keep being green and therefore chances are that nobody will feel the urge to review the checks that are performed. They are passing, so everything must be fine, right? Right?
  * **Check the wrong thing**. This one may or may not be easy to spot, depending on what they ARE checking. If it fails and the root cause of the failure is being analyzed, one of two things might happen: either the check is fixed &#8211; for example by changing the expected value &#8211; but the wrong check keeps being executed, or deeper analysis reveals that the wrong check has been performed all along and the check itself is being fixed (or even removed).

No matter what the root cause is, the end result of any of these types of check defects is the same: your checks will go bad and will therefore be worthless.

<a href="http://www.ontestautomation.com/do-you-check-your-automated-checks/good_quality_or_bad_checks/" rel="attachment wp-att-1259"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/02/good_quality_or_bad_checks.png" alt="High quality software or just bad checks?" width="604" height="453" class="aligncenter size-full wp-image-1259" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/02/good_quality_or_bad_checks.png 604w, https://www.ontestautomation.com/wp-content/uploads/2016/02/good_quality_or_bad_checks-300x225.png 300w" sizes="(max-width: 604px) 100vw, 604px" /></a>

And I bet that&#8217;s not what you had in mind when you created all of these automated scripts! Also, this problem isn&#8217;t solved by simply looking at the output report for your automated test runs, as doing so won&#8217;t tell you anything about the effectiveness of your checks. It will only tell you what percentage of your checks passed, which tells only half the story.

_2. Your check coverage will go bad_  
Another problem with the effectiveness of your checks can be seen when we look at it from another perspective:

  * **You may have implemented too many checks**. Yes, this is actually possible! This increases maintenance efforts and test run (and therefore feedback) time. This is especially significant for UI-driven checks performed by tools such as Selenium WebDriver.
  * **You may have implemented too few checks**. This has a negative impact on coverage. Not that coverage is the end all and be all of it (far from that), but I&#8217;m sure we can agree that not doing enough checks can&#8217;t be a good thing when you want to release a quality product..

I think both of these reasons warrant a periodic check validation. In such a validation, all checks performed by your automated test suite should be evaluated on the characteristics mentioned above. Checks that are no longer useful should be rewritten or removed, and any shortcomings in your test coverage should be addressed.

<a href="http://www.ontestautomation.com/do-you-check-your-automated-checks/check_your_automated_checks/" rel="attachment wp-att-1258"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/02/check_your_automated_checks.jpg" alt="Check your automated checks!" width="800" height="600" class="aligncenter size-full wp-image-1258" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/02/check_your_automated_checks.jpg 800w, https://www.ontestautomation.com/wp-content/uploads/2016/02/check_your_automated_checks-300x225.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/02/check_your_automated_checks-768x576.jpg 768w" sizes="(max-width: 800px) 100vw, 800px" /></a>

**Automating the check validation**  
Of course, since we&#8217;re here to talk about test automation, it would be even better to perform such a validation in an automated manner. But is such a thing even possible?

_Unit test level checks_  
Turns out that for checks at the unit test level (<a href="https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid" target="_blank">which should make up a significant part of your checks anyway</a>) checking your checks can be done using <a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/" target="_blank">mutation testing</a>. Even better, one of the main purposes of mutation testing IS to check the validity and usefulness of your checks. A perfect match, so to say. I am still pretty new to the concept of mutation testing, but so far I have found it to be a highly fascinating and potentially very powerful technique (if applied correctly, of course).

_API and UI level checks_  
Unfortunately, mutation testing is mainly geared towards unit and (to a lesser extent) unit integration tests. I am not aware of any tools that actively support check validation for API and UI tests. If anyone reading this knows of a tool, please do let me know!

One reason such a tool might not exist is that it would be much harder to apply the basic concept of mutation testing to API and UI-level checks, if only because you would need to:

  1. Mutate the compiled application or the source code (any subsequently compile it) to create a mutant
  2. Deploy the mutant
  3. Run tests to see whether the mutant can be killed

for every possible mutant, or at least a reasonable subset of all possible mutants. As you can imagine, this would require a lot of time and computation power, which would have a negative effect on the basic principle of fast feedback for automated checks.

One possible approach that covers at least part of the objectives of mutation testing would be to negate all of your checks and see if all of them fail. This might give you some interesting information about the defect finding capabilities of your checks. However, it doesn&#8217;t tell you as much about the coverage of your checks though, so this very crude approach would be only part of the solution.

The above does give yet another reason for implementing as much of the checks as you can in unit tests, though. Not only would they be easier to create and faster to run, but their usefulness can also be proven much more precisely in an automated manner (i.e., using mutation testing).

So, as a wrap-up, do you check your automated checks? If so, how? I&#8217;m very curious to read new insights on this matter!

**P.S.:** <a href="https://twitter.com/FriendlyTester" target="_blank">Richard Bradshaw</a> wrote a great blog post on the same topic recently, I highly suggest you read it as well over <a href="http://www.thefriendlytester.co.uk/2016/02/why-was-this-check-created.html" target="_blank">here</a>.