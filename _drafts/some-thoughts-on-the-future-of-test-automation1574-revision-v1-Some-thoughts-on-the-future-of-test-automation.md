---
id: 1575
title: Some thoughts on the future of test automation
date: 2016-08-24T13:51:12+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1574-revision-v1/
permalink: /1574-revision-v1/
---
As an independent consultant, keeping up with current trends in the field (in this case test automation, but this goes for all consultants regardless of the field they&#8217;re working in) is key to remaining relevant, hireable and able to land interesting projects and other gigs (writing, speaking, etc.). For me, the annual summer holiday is a great time to take a step back and reflect on what I&#8217;ve been working on the past year, and the trends I&#8217;ve seen emerge. In this post, I&#8217;d like to share some of the thoughts that have come up.

**Test automation is getting closer to software development**  
The first observation I&#8217;ve made from the last couple of projects I&#8217;ve been working on is that test automation is moving closer to software development in two ways:

  * Test automation is regarded more and more as software development, including the use of development patterns and practices, and
  * test automation is adopted and executed more and more by developers

The first way is definitely a good thing. I&#8217;ve been saying this for some time now &#8211; and brighter persons have done so long before me &#8211; but test automation is finally starting to be seen as a proper software development task.

I believe that the second way is beneficial too (if only for the fact that developers are best at developing software, of which test automation is a form as we&#8217;ve just stated), although there are some side notes that need to be made. First of all, completely leaving test automation to developers probably isn&#8217;t a good thing &#8211; at least not yet. There&#8217;s the effects caused by developers marking their own paper, as well as the risk of losing the testers mindset. Angie Jones wrote a <a href="http://angiejones.tech/why-developers-should-not-lead-your-automation-efforts/" target="_blank">great blog post</a> about this recently, which I think you should all read. I believe this means that the demand for proper test automation specialists (not being developers) will remain or even increase for the foreseeable future. Developers can work out the technical details for the test automation solution, something which they tend to like doing best in my experience, while the test automation specialists provides input, oversees that test automation remains efficient and meets business needs and helps with designing, development and implementation where necessary.

**Can we please stop testing through the user interface?**  
I&#8217;ve elaborated on this in a recent <a href="http://www.ontestautomation.com/an-approach-to-test-your-user-interface-more-efficiently/" target="_blank">blog post</a>, but the amount of questions I see related to user interface-driven test automation (especially related to Selenium) keeps surprising me. Yes, I know a lot of current applications are web applications, but I refuse to believe that there aren&#8217;t more efficient ways to apply test automation to at least part of these applications other than automating all kinds of scenarios through the user interface. Again: please only use user interface-driven test automation when:

  * you&#8217;re actually testing the user interface itself, or
  * you&#8217;re performing end-to-end application tests (and these tests should make up only a small part of your overall test set)

In all other situations, please try and find more effective means of driving automated tests. This will result in faster, more stable and probably better maintainable test suites, which in turn will lead to a more positive attitude towards your efforts and test automation in general. The community thanks you.

**The death of codeless test automation &#8216;solutions&#8217; (finally!)**  
Also, my crystal ball told me that &#8211; finally &#8211; everybody starts to see that codeless test automation solutions just aren&#8217;t worth it. The points I made above only reinforce this statement. I&#8217;m seeing less and less advertising for these types of tools, and I think that&#8217;s a good thing. In my experience, these tools often do not live up to the hype past the first sales demo or two. Simply put: test automation equals software development equals writing code.

**Increase of test automation scope**  
Finally, I think that the scope of test automation efforts is expanding beyond replicating the interaction between a user or a system and the application under test in an automated manner. Eloquently argumented by Richard Bradshaw in his presentation at the 2016 Test Automation Day, test automation is so much more than that. Automatically parsing log files generated through an exploratory testing session? That&#8217;s test automation. A script to set up or clear test data before or after a test session? That&#8217;s test automation. A tool that helps recording and sharing observations made when testing? You get the point. There&#8217;s so much to gain through smart applicatin of test tools other than simply automating predefined test scripts..

Please note that the above is not meant as a complete representation of my vision on the future of test automation, but rather as a list of a couple of thoughts I&#8217;ve had over a decent glass of wine (or two) during the holiday. Got any predictions about the recent or distant future of the test automation field yourself? Thought of anything brilliant during your own holiday? Please share in the comments!