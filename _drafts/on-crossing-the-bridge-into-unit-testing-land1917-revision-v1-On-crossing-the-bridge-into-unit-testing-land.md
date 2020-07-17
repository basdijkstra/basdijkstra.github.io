---
id: 1938
title: On crossing the bridge into unit testing land
date: 2017-07-28T07:41:36+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1917-revision-v1/
permalink: /1917-revision-v1/
---
Maybe it&#8217;s just the people and organizations I meet and work with, but no matter how active they&#8217;re trying to implement automation and involve testers therein, there&#8217;s one bridge that&#8217;s often too far for those that are tasked with test automation, and that&#8217;s the bridge to unit testing land. When asking them for the reasons that testers aren&#8217;t involved in unit testing, I typically get one (or more, or all) of the following answers:

  * _&#8216;That&#8217;s the responsibility of our developers&#8217;_
  * _&#8216;I don&#8217;t know how to write unit tests&#8217;_
  * _&#8216;I&#8217;m already busy with other types of automation and I don&#8217;t have time for that&#8217;_

While these answers might sound perfectly reasonable to some, I think there&#8217;s something inherently wrong with all of them. Let&#8217;s take a look:

  * With more and more teams becoming multidisciplinary, we can&#8217;t simply shift responsibility for any task to a specific subgroup. If &#8216;we&#8217; (i.e., the testers) keep saying that unit testing is a developer&#8217;s responsibility, we&#8217;ll never get rid of the silos we&#8217;re trying to break down.
  * While you might not know how to actually write unit tests yourself, there&#8217;s a lot you CAN do to contribute to their value and effectiveness. Try reviewing them, for example: has the developer of the unit test missed some obvious cases?
  * Not having time to concern yourself with unit testing reminds me of the picture below. Really, if something can be covered with a decent set of unit tests, there really is no need to write integration or even (shudder) end-to-end tests for it.

<a href="http://www.ontestautomation.com/on-crossing-the-bridge-into-unit-testing-land/toobusy/" rel="attachment wp-att-1918"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/07/toobusy.jpg" alt="Are you too busy to pay attention to unit testing?" width="710" height="270" class="aligncenter size-full wp-image-1918" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/07/toobusy.jpg 710w, https://www.ontestautomation.com/wp-content/uploads/2017/07/toobusy-300x114.jpg 300w" sizes="(max-width: 710px) 100vw, 710px" /></a>

I&#8217;m not a devotee of the test automation pyramid per se, but there IS a lot of truth to the concept that a decent set of unit tests should be the foundation of every solid test automation effort. Unit tests are relatively easy to write (even though it might not look that way to some), they run fast (no need for waiting until web pages are loaded and complete their client-side processing, for example..) and therefore they&#8217;re the best way to provide that fast feedback that development teams are looking for those in this age of Continuous Integration / Delivery / Deployment / Testing / Everything / &#8230; .

To put it in even bolder terms, as a tester, I think you have the responsibility of familiarizing yourself with the unit testing activities that your development team is undertaking. Offer to review them. Try to understand what they do, what they check and where coverage could be improved. Yes, this might require you to actually talk to your developers! But it&#8217;ll be worth it, not just to you, but to the whole team and, in the end, also to your product and your customers. Over time, you might even write some unit tests yourself, though, again, that&#8217;s not a necessity for you to provide value in the land of unit testing. Plus, you&#8217;ll likely learn some new tricks and skills by doing so, and that&#8217;s always a good thing, right?

For those of you looking for another take on this subject, John Ruberto wrote an article called &#8216;<a href="https://www.stickyminds.com/article/100-percent-unit-test-coverage-not-enough" target="_blank">100 percent unit test coverage is not enough</a>&#8216;, which was published on StickyMinds. A highly recommended read. 

_P.S.: Remember Tesults, the SaaS solution for storing and displaying test results <a href="http://www.ontestautomation.com/managing-and-publishing-test-results-with-tesults/" target="_blank">I wrote about a couple of months ago</a>? The people behind Tesults recently let me know they now offer a free forever plan as well. So if you were interested in using their services but could not afford or justify the investment, it might be a good idea to check their new plan out <a href="https://www.tesults.com/#plansview" target="_blank">here</a>. And again, I am in no way, shape or form associated with, nor do I have a commercial interest in Tesults as an organization or a product. I still think it&#8217;s a great platform, though._