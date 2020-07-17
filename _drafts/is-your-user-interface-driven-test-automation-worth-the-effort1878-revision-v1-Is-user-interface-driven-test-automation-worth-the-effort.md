---
id: 1879
title: Is user interface-driven test automation worth the effort?
date: 2017-05-30T06:34:48+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1878-revision-v1/
permalink: /1878-revision-v1/
---
I don&#8217;t know what&#8217;s happening on your side of things, dear reader, but in the projects I&#8217;m currently working on, and those that I have been working on in the recent past, there&#8217;s been a big focus on implementing user interface-driven test automation, almost always using some implementation of Selenium WebDriver (be it Java, C# or JavaScript). While that isn&#8217;t a bad thing in itself (I think Selenium is a great and powerful tool), I&#8217;m sometimes wondering whether all the effort that is being put into creating, stabilizing and maintaining these scripts is worth the effort in the end.

Recently, I&#8217;ve been thinking and talking about this question especially often, either when teaching different forms of my test automation awareness workshop, giving a talk on trusting your automation or just evaluating and thinking about my own work and projects. Yes, I too am sometimes guilty of getting caught up in the buzz of creating those perfectly stable, repeatable and maintainable Selenium tests, spending hours or sometimes even days on getting it right, thereby losing sight of the far more important questions of &#8216;why am I creating this test in the first place?&#8217; and &#8216;will this test pay me back for the effort that I&#8217;m putting into creating it?&#8217;.

Sure, there are plenty of valid applications for user interface-driven tests. Here&#8217;s a little checklist that might be of use to you. Feel free to critique or add to it in the comments or via email. In my opinion, it is likely you&#8217;re creating a valuable test if all of these conditions apply:

  * The test simulates how an end user or customer interacts with the application and receives feedback from it (example: user searches for an item in a web shop, adds it to the cart, goes through checkout and receives feedback on the successful purchase)
  * There&#8217;s significant risk associated with the end user not being able to complete the interaction (example: not being able to complete a purchase and checkout leads to loss of revenue)
  * There&#8217;s no viable alternative available through which to perform the interaction (example: the web shop might provide an API that&#8217;s being called by the UI throughout the process, but this does not allow you to check that the end user is able to perform said interaction via the user interface. Web shop customer typically do not use APIs for their shopping needs..)
  * The test is repeatable (without an engineer having to intervene with regards to test environments or test data)

Checking all of the above boxes is no guarantee for a valuable user interface-driven test, but I tend to think it is far more likely you&#8217;re creating one if it does. 

At the other end of the spectrum, a lot of useless (or &#8216;less valuable&#8217; if you want the PC version) user interface-driven tests are created. And there&#8217;s more than one type of &#8216;useless&#8217; here:

  * Tests that use the UI to test business logic that&#8217;s exposed through an API (use an API-level test instead!) or implemented in code (how about those unit tests?). Not testing at the right level supports shallow feedback and increased execution time. Goodbye fast feedback.
  * Tests that are unreliable with regards to execution and result consistency. &#8216;Flaky&#8217; is the word I see used a lot, but I prefer &#8216;unreliable&#8217; or &#8216;untrustworthy&#8217;. &#8216;Flaky&#8217; sounds like snow to me. And I like snow. I don&#8217;t like unreliable tests, though. And user interface-driven tests are the tests that are most likely to be unreliable, in my experience.

What it boils down to is that these user interface-driven tests are by far the hardest to implement correctly. There&#8217;s so much to be taken care of: waiting for page loads and element state, proper exception handling, test data and test environment management. Granted, those last two are not limited to just this type of tests, but I find that people that know how to work on the unit or API level are also far more likely to be able to work with mocks, stubs and other simulations to deal with issues related to test data or test environments.

Here&#8217;s a tweet by Alan Page that recently appeared in my timeline and that sums it all up pretty well:

<blockquote class="twitter-tweet" data-width="500" data-dnt="true">
  <p lang="en" dir="ltr">
    Idiotic that we take the most difficult thing to do in test, ui automation, and use it as an entry point for those who want to learn to code
  </p>
  
  <p>
    &mdash; b-alan-cé (@alanpage) <a href="https://twitter.com/alanpage/status/866323176317308929?ref_src=twsrc%5Etfw">May 21, 2017</a>
  </p>
</blockquote>



So, having read this post, are you still sure that all these hours you&#8217;re putting into creating, stabilizing and maintaining your Selenium tests are worth it in the end? If so, I tip my hat to you. But for the majority of people working on user interface-driven tests (again, including myself), it wouldn&#8217;t hurt to take a step back every now and then, lose the &#8216;have to get it working&#8217; tunnel vision and think for a while whether your tests are actually delivering enough value to justify the efforts put into creating them.

So, are your UI automation efforts worth it?