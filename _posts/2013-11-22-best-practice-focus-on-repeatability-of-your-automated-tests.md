---
id: 254
title: 'Best practice: focus on repeatability of your automated tests'
date: 2013-11-22T14:10:52+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=254
permalink: /best-practice-focus-on-repeatability-of-your-automated-tests/
categories:
  - Best practices
tags:
  - best practice
  - repeatability
  - test automation
---
_This is the first installment in a series of posts on test automation best practices. Notwithstanding the rapid growth and evolution of the test automation field, a number of best practices can be identified that stand the test of time. Adhering to these best practices will improve the added value of your automated tests, no matter the scale or scope of the test, or the technology or tools that are used to design, implement or execute automated tests._

In order for your automated test suites to be truly efficient, they should be set up with **repeatability** in mind. This means that your tests should execute with the click of a button, or by entering a command in the command prompt, again and again, without the need for manual intervention during or in between test runs. They should also yield the same (or comparable) test results every single time. Except when the system under test changes or fails, of course.

To achieve or improve repeatability, you need to pay attention to a number of things during the implementation of your automated tests. I will address some of these in this article. There are probably lots of other aspects to be considered, but these stand out for me.

_One disclaimer_: the repeatability factor does not apply (or applies to a far lesser extent) to projects where there&#8217;s just a single test run to be executed, for instance after a conversion or a migration project. If you&#8217;re involved in such a project, it&#8217;s probably not worth it to put extra effort in achieving repeatability of your automated tests.

**Start small**  
As with every software development project, it is best to start small when implementing automated tests. Automate one or two test cases, or even just one or two steps of the process to be automated, and execute them over and over again to make sure they are stable and repeatable. Once your small test cases are proven to be repeatable you can build on them to create larger test suites. Make sure you prove that every major change you make to your tests does not compromise the repeatability of your tests.

**Watch your test data**  
An important issue when designing and implementing repeatable automated tests is the use of test data. Scripts that alter or consume test data need some extra attention with regards to repeatability. A test data object, such as a customer, an order, etc., used in a certain test run may be altered or removed during that run, rendering it unsuitable or unavailable for subsequent test runs.

Roughly speaking, there are three possible approaches for dealing with test data that is altered during a test run:

  * Create the test data during the test run. For example, if your test script covers the processing of an order, have your script create a new order before processing it to make sure there&#8217;s always an order to be processed
  * Reset the test data to its original state after the test run. For example, if your test script covers changing a customer&#8217;s address to a foreign location, reset it to its original value after the test script has been executed (through whichever interface available).
  * Select the test data to be used at the start of your test run. Rather than using previously defined sets of test data, have your script perform a query on the available test data set to select a test data object to use in a particular test run. Make sure that your script can handle occasions where there&#8217;s no suitable test data object available.

**Be ready for continuous integration**  
With the current trend of test and development teams working closer together in increasingly shorter development and test cycles (think Agile / Scrum and DevOps), continuous integration (CI) is applied not only for development tasks, but for system and integration testing as well. In order to be able to keep up with the development team, automated tests should seamlessly integrate with the continuous integration platform in use. Most open source and COTS automated test tools provide a command line interface to execute test runs and export and distribute test result reports. This doesn&#8217;t make your test scripts automagically suited for use in a CI environment. Only test scripts or frameworks that can be run again and again without the need for manual intervention can be successfully integrated in the CI process, so make sure yours fit the bill!

<div id="attachment_260" style="width: 1065px" class="wp-caption aligncenter">
  <a href="http://www.ontestautomation.com/wp-content/uploads/2013/11/ci_schematic.png"><img aria-describedby="caption-attachment-260" src="http://www.ontestautomation.com/wp-content/uploads/2013/11/ci_schematic.png" alt="A schematic representation of continuous integration" width="1055" height="542" class="size-full wp-image-260" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/ci_schematic.png 1055w, https://www.ontestautomation.com/wp-content/uploads/2013/11/ci_schematic-300x154.png 300w, https://www.ontestautomation.com/wp-content/uploads/2013/11/ci_schematic-1024x526.png 1024w" sizes="(max-width: 1055px) 100vw, 1055px" /></a>
  
  <p id="caption-attachment-260" class="wp-caption-text">
    A schematic representation of continuous integration
  </p>
</div>

**Effects of repeatability on the acceptance and the ROI of test automation**  
Once you have managed to control the repeatability of your automated test scripts, you should see some pretty positive results with regards to the acceptance of automated testing and the ROI associated with the test automation project:

  * Repeatable tests can be run on demand, as often as required, leading to a dramatic reduction of the cost per test run and the time needed to complete a development/test cycle
  * Automated tests that can run unattended and that can be repeated on demand will appeal to everybody from developers to upper management, increasing its perceived value and ultimately also increasing the trust in the product delivered by your team.

Are your tests as repeatable as they can be? Let me know how you achieved your degree of repeatability and the issues you had to overcome!