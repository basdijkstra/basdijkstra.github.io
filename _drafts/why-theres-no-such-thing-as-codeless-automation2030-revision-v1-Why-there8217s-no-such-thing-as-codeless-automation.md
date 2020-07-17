---
id: 2031
title: 'Why there&#8217;s no such thing as codeless automation'
date: 2017-09-25T12:06:16+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/2030-revision-v1/
permalink: /2030-revision-v1/
---
In today&#8217;s blog post &#8211; which, again, is really nothing more than a thinly veiled rant &#8211; I&#8217;d like to cover something that&#8217;s been covered before, just not by me: codeless test automation and why I think there isn&#8217;t and should not be such a thing.

I&#8217;ve seen numerous &#8216;solution&#8217; vendors advertise their products as &#8216;codeless&#8217;, implying that everybody in the team will be able to create, run and maintain automated tests, without having to, well, write code. I&#8217;ve got a number of problems with selling test automation in this way.

**It&#8217;s not codeless. It&#8217;s hiding code.**  
The first gripe I have with &#8216;codeless&#8217; automation is a semantic one. These solutions aren&#8217;t codeless at all. They simply _hide_ the code that runs the test from plain sight. There are no monkeys in the solution that magically execute the instructions that make up a test. No, those instructions are translated _into actual code_ by the solution, then executed. As a user of such a solution, you&#8217;re still coding (i.e., writing instructions in a manner that can be interpreted by a machine), just in a different syntax. That&#8217;s not codeless.

**While it might be empowering, it&#8217;s also limiting.**  
Sure, using codeless tools might potentially lead to more people contributing to writing automated tests (although from my experience, that&#8217;s hardly how it&#8217;s going to be in the end). The downside is: it&#8217;s also limiting the power of the automated tests. As I said above, the &#8216;codeless&#8217; solution is usually nothing more than an abstraction layer on top of the test automation code. And with abstraction comes loss of detail. In this case, this might be loss of access to features of the underlying code. For example, if you&#8217;re using a codeless abstraction on top of Selenium, you might lose access to specific waiting, synchronization or error handling mechanisms (which are among the exact things that makes Selenium so powerful).

It might also be loss of access to logging, debugging or other types of root cause analysis tools, which in turn leads to shallower feedback in case something goes wrong. While the solution might show you that something has gone wrong, it loses detail on where things went wrong and what caused the failure. Not something I like.

Finally, it might also limit access to hooks in the application, or limit you to a specific type of automated tests. If such a solution makes it potentially easier to write automated tests on the user interface level, for example, there&#8217;s significant risk that all tests will be written at that level, even though that might not be the most efficient approach in the first place. If all you&#8217;ve got is a hammer&#8230;

**It&#8217;s doing nothing for the hard problems in creating maintainable automation.**  
Let&#8217;s face it: while writing code might seem hard to people that haven&#8217;t done it before, it actually isn&#8217;t that difficult once you&#8217;ve had a couple of basic programming classes, or followed a course or two on <a href="https://www.codecademy.com/" rel="noopener" target="_blank">Codecademy</a>. What _is_ hard is writing good, readable, maintainable code. Applying SOLID and DRY principles. Structuring your tests. Testing the right thing at the right level. Creating a solid test data and test environment strategy. _Those_ things are hard. And codeless test automation does nothing for those problems. As I tried to make clear in the previous paragraphs, it&#8217;ll often make it even harder to solve those problems effectively.

I&#8217;m all for creating solutions that make it easier to write, run and maintain automation. I hate people selling solutions as something they&#8217;re not. Codeless test automation is not going to solve your test automation problems. People that know

  * how to decide what good automation is
  * how to write that automation, and
  * how to pick the tools that will help them achieve the goals of the team and organization

will.