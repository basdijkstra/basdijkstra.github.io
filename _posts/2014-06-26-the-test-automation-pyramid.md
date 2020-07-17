---
id: 502
title: The test automation pyramid
date: 2014-06-26T09:53:18+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=502
permalink: /the-test-automation-pyramid/
categories:
  - Best practices
  - General test automation
tags:
  - mike cohn
  - test automation pyramid
---
In his book Succeeding with Agile, <a href="https://www.mountaingoatsoftware.com/" target="_blank" rel="noopener noreferrer">Mike Cohn</a> describes the concept of a test automation pyramid, describing three levels of test automation, their relation and their relative importance. As an advocate of [minimizing user interface-based test automation](http://www.ontestautomation.com/best-practice-use-ui-driven-automated-testing-only-when-necessary/ "Best practice: use UI-driven automated testing only when necessary") I wholeheartedly support this pyramid, which is why I decided to share it with you at ontestautomation.com.

Graphically, the test automation pyramid as proposed by Mike Cohn looks like this:  
[<img class="aligncenter size-full wp-image-503" src="http://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid.png" alt="The test automation pyramid" width="751" height="585" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid.png 751w, https://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid-300x233.png 300w" sizes="(max-width: 751px) 100vw, 751px" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid.png)  
**Base layer: unit tests**  
Unit tests form the base layer of every solid automated testing approach. They can be written relatively quickly and give the programmer very specific information about the origins of a bug, up to the exact line of code where a failure occurs. Compare this to a bug report from a tester, who would usually be more like &#8216;function X, Y and / or Z are not working when I enter A or B, now go fix it&#8217;. This often requires more analysis (reproduction, debugging) and therefore more time from the developer to fix things.

Another advantage of unit tests is that not only can they be written quickly, test execution is also very fast, giving the developer immediate feedback on code quality.

Possible drawbacks of unit tests are that they mostly focus on small pieces of code (methods, classes) and are therefore unable to detect integration or system level bugs. Also, as they are written in code, they are written mostly by developers and not by testers. Ideally, unit tests should be written by someone other than the developer of the code that is being tested.

**Top layer: user interface-level tests**  
Let&#8217;s skip the middle layer for a moment and go right to the top of the pyramid, where the UI-level automated tests reside. Ideally, you would want to do as little of this as possible, as they are often the most brittle and take the longest time both in test case development and in test execution. In my opinion, this form of test automation should only be used when the UI is actually being tested rather than the underlying system functionality, or when there is no viable alternative. Such an alternative is available more often than you&#8217;d think, by the way..

**Middle layer: service and API tests**  
For those tests that exceed the scope of unit tests it is strongly advised to use tests that communicate with the application under test at the service or API level. Most modern applications offer some sort of API (either through an actual programming interface or through a web service exposing functionality to the outside world) that can be used by the tester to test those applications. These tests are often far less brittle (as service interfaces and APIs tend to change far less often than user interfaces) and execute far quicker with less false negatives.

**The inverted test automation pyramid**  
It is no coincidence that Mike Cohn calls this middle layer the <a href="https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid" target="_blank" rel="noopener noreferrer">forgotten layer</a> of test automation. All too often, test cases that cannot be covered by developers in unit tests are directly automated on the user interface level, resulting in big sets of UI level automated tests that take eons to execute and maintain. This phenomenon is represented by an inverted test automation pyramid:  
[<img class="aligncenter size-full wp-image-505" src="http://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid_inverted.png" alt="The inverted test automation pyramid" width="745" height="582" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid_inverted.png 745w, https://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid_inverted-300x234.png 300w" sizes="(max-width: 745px) 100vw, 745px" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/pyramid_inverted.png)  
In extreme cases, the middle layer doesn&#8217;t even exist in the overall test automation approach. It doesn&#8217;t need explanation that in most test automation projects that resemble this inverted pyramid a lot of money is wasted unnecessarily on development and maintenance of the automated test cases..

**My advice**  
I&#8217;d therefore advice any test automation specialist that would like to make his or her project a success (and who doesn&#8217;t?) to do a couple of things:

  * Get familiar with unit testing, its benefits and the way the developers in your project use it. Try to understand what they test and what coverage is achieved in unit testing. Try and work with your developers to see where this coverage can be increased further and what you can do to achieve that.
  * For those tests that are not covered by unit tests, try to find out whether the application you are testing offers an API that you can use to base your automated tests on. Initially, testing &#8216;under the hood&#8217; without a UI to drive tests might seem challenging, but the benefits are definitely worth it.

Following these two suggestions will help you greatly in getting your test automation pyramid in the right shape. What does your pyramid look like?

_Final note: Although some articles on the Internet even go as far as to suggest an &#8216;ideal&#8217; mix of the pyramid layers (80% unit tests, 15% API tests and 5% UI tests, for example), I think there is no one &#8216;ideal&#8217; mix. It depends a lot on the type of application under test and the skill sets of your developers and especially your testers. However, the shape of the test automation pyramid should be roughly the same in any case._