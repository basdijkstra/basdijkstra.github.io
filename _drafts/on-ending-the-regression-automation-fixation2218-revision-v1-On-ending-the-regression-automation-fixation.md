---
id: 2225
title: On ending the regression automation fixation
date: 2018-07-04T10:00:05+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/2218-revision-v1/
permalink: /2218-revision-v1/
---
_Note: in my observation, scripted test execution and the type of regression test scripts I&#8217;m referring to are slowly going away, but a lot of organizations I work with still use them. Not every organization is full of testers working in a context-driven and exploratory way while applying CI/CD and releasing multiple times per day. If you&#8217;re working in one, that&#8217;s fine. This blog post probably is not for you. But please keep in mind that there are still many organizations that apply a more traditional, script-based approach to testing._

In the last couple of months, I&#8217;ve been talking regularly about some of the failures I&#8217;ve made (repeatedly!) during my career so far. My talk at the <a href="https://www.ontestautomation.com/on-the-2018-romanian-testing-conference/" rel="noopener" target="_blank">Romanian Testing Conference</a>, for example, kicked off with me confessing that in retrospect, a lot of the work I&#8217;ve done until all too recently has been, well, inefficient at best, and plain worthless in other cases. Only slowly am I now learning what automation really is about, and how to apply it in a more useful and effective manner than the &#8216;just throw more tools at it&#8217; approach I&#8217;ve been supporting for too long.

Today, I&#8217;d like to show you another example of things that, in hindsight, I should have been doing better for longer.

One of my stock answers to the question &#8216;Where should we start when we&#8217;re starting with automation?&#8217; would be to &#8216;automate your existing regression tests first&#8217;. This makes sense, right? Regression tests are often performed at the end of a delivery cycle to check whether existing functionality aspects have not been impacted negatively as a result of new features that were added to the product. These tests are often tedious &#8211; new stuff is exciting to test, while existing features are so last Tuesday &#8211; and often take a long time to perform, and one thing there often isn&#8217;t left is time at the end of a delivery cycle. So, automating away those regression tests is a good thing. Right?

Well, maybe. But maybe not so much.

To be honest, I don&#8217;t think &#8216;start with automating your regression tests&#8217; isn&#8217;t a very good answer anymore, if it has ever been (again, hindsight is 20/20&#8230;). It _can_ be a decent answer in some situations, but I can think of a lot of situations where it might not be. Why not? Well, for two reasons.

**Regression scripts are too long**  
The typical regression test scripts I&#8217;ve seen are looong. As in, dozens of steps with various checkpoints along the way. That&#8217;s all well and good if a human is performing them, but when they are turned into an automated script verbatim, things tend to fall apart easily.

For example, humans are very good at finding a workaround if the application under test behaves slightly differently than is described in the script. So, say you have a 50-step regression script (which is not uncommon), and at step 10 the application does something similar to what is expected, but not precisely the same. In this case, a tester can easily make a note, find a possible way around and move on to collect information regarding the remaining steps.

Automation, on the other hand, simply says &#8216;f*ck you&#8217; and exits with a failure or exception, leaving you with no feedback at all about the behaviour to be verified in steps 11 through 50.

So, to make automation more efficient by reducing the risk of early failure, the regression scripts need to be rewritten and shortened, most of the times by breaking them up in smaller, independently executed sections. This takes time and eats away the intended increase in speed expected from the introduction of automation. And on top of that, it may also frustrate people unfamiliar to testing and automation, because instead of 100 scripts, you now have to automate 300. Or 400. And that sounds like more work!

**Regression scripts are written from an end user perspective**  
The other problem with translating regression scripts verbatim is that these scripts are often written from an end user perspective, operating on the user interface of the application under test. Again, that&#8217;s all well and fine when you&#8217;re a human, but for automation it might not be the most effective way to gain information about the quality of your application under test. User interface-driven automation is notoriously hard to write and maintain, hard to stabilize, slow to execute and relatively highly prone to false positives.

Here too, in order to translate your existing regression scripts into effective and efficient automation, you&#8217;ll need to take a thorough look at what exactly is verified through those scripts, find out where the associated behaviour or logic is implemented, find or develop a way to communicate with your application under test on that layer (possibly the user interface, more likely an API, a single class or method or maybe even a database table or two) and take it from there.

Sure, this is a valuable exercise that will likely result in more efficient and stable automation, but it&#8217;s a step that&#8217;s easy to overlook when you&#8217;re given a batch of regression scripts with the sole requirement to &#8216;automate them all&#8217;. And, again, it sounds like more work, which not everybody may like to hear.

So, what to do instead?

My advice: _forget about automating your regression tests_.

There. I&#8217;ve said it.

Instead, ask yourself the following three questions with regards to your testing efforts:

  1. What&#8217;s consuming my testing time?
  2. What part of my testing efforts are repetitive?
  3. What part of my testing efforts can be repeated or enhanced by a script?

The answer(s) to these questions may (vaguely) resemble that what you do during your regression testing, but it might also uncover other, much more valuable ways to apply automation to your testing. If so, would it still make sense to aim for &#8216;automating the regression testing&#8217;? I think not.

So, start writing your automation with the above questions in mind, and keep repeating to yourself and those around you that automation is there to make your and their life easier, to enable you and them to do your work more effectively. It&#8217;s not just there to be applied everywhere, and definitely not to blindly automate an existing regression test suite.