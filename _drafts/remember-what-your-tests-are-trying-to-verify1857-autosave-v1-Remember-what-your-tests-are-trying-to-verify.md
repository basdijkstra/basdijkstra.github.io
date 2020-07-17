---
id: 1859
title: Remember what your tests are trying to verify
date: 2017-05-07T17:58:19+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1857-autosave-v1/
permalink: /1857-autosave-v1/
---
Lately, I&#8217;ve been working a lot on Selenium-based test automation solutions. And even though I&#8217;m still not overly enthusiastic about creating lots of user interface-driven automated tests, now that I&#8217;m getting more skilled at creating robust and stable Selenium tests, I am starting to appreciate the tool and what you can do with it more and more. As everybody working with Selenium can tell you, there are situations where things can get, well, interesting. And by interesting, I mean tricky. Dealing with dynamic front end frameworks and unpredictable modals and overlays can ask a lot of you in terms of debugging and exception handling skills.

Not yet being fluent in Selenese, I find myself on Google and (subsequently) StackOverflow a lot, trying to find suitable solutions for problems I encounter. While doing so, there&#8217;s one thing that I see a lot of people do, yet strikes me as really odd, given what I think is the purpose of these user interface-driven tests:

_Forcing your tests to do something your users can&#8217;t._

From what I&#8217;ve understood, studying and working in the test automation field for a while now, user interface-driven tests, such as these Selenium tests, should be used to verify that the user of your user interface is able to complete a sequence of predefined actions. If you&#8217;re working in a hip and happening environment, these are often called &#8216;customer journeys&#8217; or &#8216;user journeys&#8217;. But that&#8217;s not my point. What my point is, is that quite often I see workarounds suggested that go beyond what a regular user could do with his or her keyboard and / or mouse.

For example, take an element that is invisible until you hover over it with your mouse. If you just try to do a _click()_, your test will probably throw an exception stating that the element was not visible. Now, there are (at least) two ways to deal with this situation:

  1. Use the _Actions_ class to simulate a mouseover, then click the element.
  2. Use a _JavaScriptExecutor_ to perform the click.

While both approaches might result in a passing test, I am of the opinion that one is useful and the other is a horrible idea. Based on what I&#8217;ve written so far, can you guess which of the two options I&#8217;d suggest?

Indeed, option #1 is the way to go, for two reasons:

  * User interface-driven tests should mimic actual user interaction as closely as possible. I&#8217;ve never seen a user execute some JavaScript on a page to make an element visible. Either that, or I&#8217;m hanging around the wrong group of users..
  * What happens if a front-end developer makes a mistake (I know, they never do, but let&#8217;s assume so anyway) which causes the element not to become visible, even on a mouseover? With option #1, your test will fail, for the right reason. With #2, hello false negative!

There are some exceptions to the rule, though. The prime example I can think of is handling file uploads by directly sending the absolute path of the file to be uploaded to the _input_ element responsible for the file upload using sendKeys(), instead of clicking on it and handling the file dialog. I&#8217;ve tried the latter before, and it&#8217;s a pain, first because you can&#8217;t do it with the standard Selenium API (because the file dialog is native to the operating system), second because different browsers use different file dialog layouts, resulting in a lot of pesky code that easily breaks down. In this case, I prefer to bypass the file dialog altogether (it&#8217;s probably not the subject of my test anyway).

In (almost) all other cases though, I&#8217;d advise you to stick to simulating your end user behavior as closely as possible. The job of your user interface-driven tests, and therefore of you as its creator, is not to force a pass, but to simulate end user interaction and see if that leads to a successfully executed scenario. Don&#8217;t fool yourself and your stakeholders by underwater tricks that obscure potential user interface issues.

Remember what your tests are trying to verify.