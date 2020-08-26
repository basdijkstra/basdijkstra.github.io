---
title: Case A
layout: page
permalink: /cases/a/
---
**The organization**

This case takes place at a large and established financial services organization. Since their inception, they have operated as a project-based organization, with many long-lasting waterfall software development projects. One year ago, they have decided to adopt a Continuous Delivery approach to software development and delivery to be able to better keep up with upcoming competitors.

**The team and the product**

The team consists of 4 developers, a tester and a scrum master. They follow Scrum and work on a mission critical batch processing system that calculates monthly payments to clients based on a large set of business rules.

The system is quite complex, it is said that it will take new hires around a year to get really familiar with it. It has been around for more than 10 years, and there are very few people in the organization that know about all its nuts and bolts.

**The testing process**

Most testing is done by the tester in the team, in cooperation with business representatives who have a final say in accepting the software changes. Because of the repetition in their work, and the ever growing pressure to release faster, the team is looking to implement test automation.

The developers already adopted unit testing, and they pride themselves in maintaining a high level of code coverage, especially around the complex part involving all the calculations and business rules. Despite this, a lot of regression defects slip through and make it to the tester and the business representatives. As a result, the business does not have a lot of confidence in the work delivered by the developers.

**Automation challenges**

To increase the level of trust of all parties involved, the team decides to implement automation of acceptance tests. One developer is tasked with the development of a testing framework that can operate and test against the system under test. Unfortunately, the tester is too busy with extinguishing fires to contribute a lot, despite them having a fair level of experience with automation.

When the framework is delivered, the developer gives a demo to the team and writes some documentation on how to write and run tests using the framework. Unfortunately, that’s where this part of the automation efforts stalls. The developers rather work on features, the tester is too busy putting out fires, testing new features and communicating with business representatives to make a contribution.
