---
title: Case E
layout: page
permalink: /cases/e/
---
**The organization**

This case takes place at a small testing services provider that performs testing and test automation services in house for its clients.

**The client and the product**

For a specific project, a number of test automation engineers were tasked with automating regression tests for a webshop that sells electronic cigarettes and accessories (charger, refill capsules, etc.) to customers in the United States. As this is a highly regulated product, it’s very important to check with every deployment that all business rules that are related to persons of a certain age, living in a certain state being (un-)able to purchase a given product are correct. The fines for (either accidentally or purposefully) selling a product to a person who (by age, by zip code or both) should be prohibited to do so are significant!

**The testing process**

There are about 6500 relevant and unique combinations of age group, state and product category. A full regression test run should cover all of these combinations, making regression testing a time-consuming process. As the system under test is a web store, they want to release early and often to stay ahead of the competition, though, so in practice, regression tests were often skipped. However, since the client base is growing, so is the risk of violations of regulations around electronic cigarettes, a risk the web shop is no longer willing to take.

**Automation challenges**

Since the web shop is developed by a team from yet another organization in another part of the world, all that the test automation engineers have access to at the moment is a version of the web shop deployed in a test environment and documentation about the business rules that need to be verified.

Before the demand for a regression test that fully covered the aforementioned 6500 cases, a test suite with a much smaller coverage was built. Part of that test suite was an end-to-end test, running against the test environment, that performed a search on the web site, put a product in a shopping cart, proceeded to checkout and filled in all the necessary forms to place an order, so that the test automation engineers could demonstrate that the web site supported the primary buying process, or at least that it did under those specific circumstances.

The test automation engineers then opted to make that test script data driven, i.e., perform it 6500 times, with different input parameters for product, zip code and date of birth, so as to check the correctness of the business rules in that way. They soon ran into trouble: one iteration took about 45 seconds from start to finish to complete. When they wanted to run it 6500 times, it took them (give or take) 292500 seconds (= 4875 minutes = 81,25 hours = 3,4 days). And that’s when the entire test run finished flawlessly, which it never did..
