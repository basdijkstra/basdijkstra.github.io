---
title: Case D
layout: page
permalink: /cases/d/
---
**The organization**

This case takes place at a medium-sized telecommunications company, delivering triple play (Internet, television, landline telephone) subscriptions to consumer clients. All software is developed in house.

**The team and the product**

At the moment, there’s a very traditional organizational structure in place. Software development is done on one floor of the building, testing is done by a separate testing team that is located on a different floor. The testing team is responsible for testing the entire business process around provisioning new subscriptions, upgrading and renewing them as well as cancelling, a process that involves interacting with at least 5 different applications, as well as some time traveling (for example to skip cool off periods).

**The testing process**

Because there are quite a few variations in products and processes, there is a significant regression test suite that is dutifully maintained and executed by the test team. Each release, the team has to go through the entire regression test set (around 150 test scripts, of which most take 10 minutes or more to complete due to the length and complexity of the processes). To save time and money, the test manager decides to implement test automation.

**Automation challenges**

Experienced test automation engineers are tasked with the automation of the existing regression test scripts. Because of the variety of applications under test, a multipurpose vendor-based tool is selected to help perform this task. To keep a close eye on the progress of the automation of the regression test scripts, an Excel sheet has been created in which the status of the test automation efforts can be administered. Red means ‘to do’, orange is ‘in progress’ and green means ‘done’. A test script is considered ‘done’ when it has run in the test environment without errors for three nights in a row.

The project scope is being strictly monitored: the list of regression test scripts to be automated is frozen and the project will be considered finished when all scripts have been automated.

The test automation experts start this new project being excited. Progress is being made and soon enough the first short regression test scripts can be promoted to the ‘done’ status.
After a while, though, things are slowly starting to take a turn for the worse: scripts that used to run fine now fail.

The longer regression test scripts turn out to be hard to complete; with each test run these scripts fail at some point, to the frustration of the automation engineers.
In the end, around 60% of the original set of test scripts can be marked as ‘done’. The project is brought to an end, but in the end nobody is really happy with the end result…
