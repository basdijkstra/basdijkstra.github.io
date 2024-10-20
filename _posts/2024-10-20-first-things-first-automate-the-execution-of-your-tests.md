---
title: First things first - automate the execution of your tests
layout: post
permalink: /first-things-first-automate-the-execution-of-your-tests/
categories:
  - Test automation
tags:
  - small steps
  - continuous integration
  - version control
---
> "Well, it works on my machine"

I'm sure we've all heard a developer say that to us at some point in our career, and we all roll our eyes at them in response. It clearly doesn't work on _your_ machine...

We would do well to apply that exact same philosophy of 'it shouldn't just run on my machine' to our tests: everybody in the team should be able to run them. Test automation is all about fast feedback, and you'll only get that when running your tests is quick, smooth and seamless.

Also, tests should be able to run on demand, without having to wait for someone to set up a machine, prepare necessary test data, and so on. No matter if 'on demand' means once per sprint or ten times per day in your context, your tests should be ready to go.

Running tests on demand, on someone else's machine, is therefore one of the first things we cover when I work with individuals and teams when they learn test automation, or when we're improving what they already know.

That way, we don't have to do a lot of work later on, when we wrote dozens or even hundreds of tests, to automate the running of our test and make it that quick, smooth and seamless experience we want it to be.

Here's a step-by-step guide that I follow pretty much to the letter every time I teach test automation to individuals or small teams, for example in my [corporate mentoring](/mentoring/).

### Write a couple of small but meaningful tests

They don't have to be your most important tests. Not at all. But as there's no point in writing useless tests, pick something that should 'just work'. For UI tests, the login sequence to your application works. For an API test, pick a call that retrieves some data from a backend system, preferably something that you know should always be there. Bonus points when the API call requires authentication. For unit tests, write a test for a meaningful part of the behaviour of your code. No need to test getters / setters.

Make sure those tests run reliably on your local machine by running them a few times using the command line. Why the command line? Because that's how your tests will be run in CI in a later step, so better make sure you know how to do this from the start.

Your tests should behave the same way every time and report the same results every time. The code itself doesn't have to be pretty, yet, we'll get to that later.

_What this teaches you: writing tests, working with testing libraries_

### Bring your tests under version control

Most code (and that includes tests) requires collaboration, and the only reliable and scalable way to collaborate on code is through version control. So, the logical next step is to bring your tests under version control. Make sure to exclude / ignore the parts of your code base that should not be version controlled, including

* compiled code and other generated artifacts
* test reports
* credentials and other secrets

In the case of credentials and other secrets, deal with them in the way your development team does already. Typically, this involves some kind of keystore or secrets vault and/or using (environment) variables.

_What this teaches you: working with version control systems, dealing with secrets in your code_

### Add test execution to a CI pipeline

Maybe there's an existing build and deploy pipeline you can add your tests to, maybe you'll need to build a separate pipeline for now. Both are fine, although ideally you want your tests to become a stage in the 'main' pipeline for your application at some point.

Either way, make sure your tests run every time a CI build is triggered. This can be a time-based trigger ('run tests every night at 11 PM'), an event-based trigger ('run these tests every time someone commits to a branch / every time someone creates a pull request') or both. As long as your tests get run at least once a day, it's all good. You want those tests to run often to get fast feedback about two things:

* the state of the application you're verifying with these tests, and
* the reliability of your tests themselves

This second bullet is really important - we want to start building trust in our tests from the get go, and the only way to do that is to run them often on someone else's machine: through CI.

_What this teaches you: building / editing CI pipelines, configuring and working with CI agents_

### Parallel test execution

We're in the very early stages yet, but at some point our test suite is going to grow, and test execution will take longer and longer. However, ideally we want the feedback from a test run to come back to us as soon as possible. I typically use 'the time it takes to get a cup of coffee' as a rule of thumb here.

Especially when your test suite is heavy on the E2E tests, you'll quickly see test execution time skyrocket. Running tests in parallel is a great way to deal with this, and it is by far the easiest to achieve when your test suite is still in its infancy. Some frameworks support parallel test execution 'out of the box', for others, it is a little more work, but implementing parallelization early on will save you a lot of headache later, so now is the right time to do it.

_What this teaches you: running tests in parallel, dealing with concurrency conflicts_

### Next steps

Only after we have written our first tests, brought them under version control, automated the execution of our tests using a CI orchestrator and made sure our tests can run in parallel without issues, it is time to work on other tasks, including:

* improving the readability and maintainability of our existing test code
* adding reports and notifications on test results
* make sure our tests can run against multiple environments (if needed)

Of course, you'll also spend a lot of time (most of your time, actually) adding more tests to your test suite. But please, do yourself a favour, and only start doing that when you have completed the previous steps. You'll thank yourself (and me ;) later on for doing so and for setting yourself up for success early on. 