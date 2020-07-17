---
id: 459
title: Five considerations before choosing an automated testing solution
date: 2014-06-07T08:10:50+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=459
permalink: /five-considerations-before-choosing-an-automated-testing-solution/
categories:
  - General test automation
tags:
  - test automation
  - tool selection
---
Selecting a tool to aid your project or organisation with the implementation of automated software testing can be a daunting task. Many tools are available, each of them with its own benefits and drawbacks. The following considerations will provide you with some much-needed guidance in the test tool selection process.

**What kind of technology is to be automated?**  
The choice for a specific automated testing tool heavily depends on the type of application(s) your tests are written for. Many tools are specifically designed for a single type of application, such as Selenium, which is targeted towards websites and web applications running in a browser. Other tools are able to handle a lot of different types of applications, such as browser applications, Windows applications and SAP. Tools that fall into the latter category tend to be more expensive, but might be the better choice if your test scripts cover different types of applications.

**Do I go for open source or for commercially licensed tooling?**  
At first sight, going the open source route when selecting an automated testing tool might seem the most cost-effective option, due to the lack of license fees and support contract fees that typically come with commercial test tools. However, open source tools come with necessary investments of their own, as it often takes more time and more technical (programming) knowledge to set up and maintain automated test scripts. One often overlooked advantage of open source test tooling is the fact that the more popular ones are often supported a large community on the Internet. This community can be tapped into for technical support and often offers a library of additional features and extensions for the tool in case.

**Do I prefer in house implementation or do I want to outsource test automation activities?**  
Another consideration to make is whether to have your own team implement the automated test scripts or to leave this to a third party. Keeping automated test development in house has a number of benefits, such as:

  * The people working on test automation are likely to be very familiar with the application(s) under test.
  * Knowledge gained on test automation is retained within the organisation.
  * The opportunity to work on test automation might be a good motivator for people in the organisation willing to learn something new.

On the other hand, outsourcing test automation implementation also has certain benefits, the most important being that dedicated test (automation) service providers likely have a lot of experience with the selected tool, enabling them to implement it much more efficiently.

**How much training is required for the implementation of test automation?**  
Some tools require more technical knowledge upfront before users can successfully automate tests with it, while others do all they can to abstract from the technical details and offer an interface that makes it possible to implement and run automated tests for everybody. The former category of tools typically requires more training for them to be used. Another factor to be considered is the programming or scripting language used by the automated test tool. If, for instance, a tool is Java-based and all you have in your company are PHP developers, more training might be required than when your software development department – and the testers included in it – works with Java on a daily basis.

**Does the tool match our current software testing process?**  
Finally, another thing worth considering is the extent to which the tool matches your current software testing process. Questions that you might want to ask with respect to this are:

  * Do the reports produced by the tool match the information requirements from the software development team and from management?
  * Does the tool integrate nicely with my current or desired automated build or continuous integration software delivery setup?
  * Can scripts be run by everybody within the software development and testing team? Note that this differs from who is going to develop the scripts.

When there is a mismatch between the features of the tool under consideration and your current software testing process, there are two further options. One is to consider a different tool that better fits the process, the other is to adapt the process to fit around the tool. The former is generally the more preferable, but in some cases where the testing tool fits the software to be tested perfectly, it might be worthwhile to adapt your testing process in some points to get a good match between the two.