---
title: The 'valuable' in valuable feedback, fast
layout: post
permalink: /the-valuable-in-valuable-feedback-fast/
categories:
  - Valuable feedback fast
tags:
  - Test automation
  - Training
---
When I talk about the goals and the purpose of test automation, I often use the phrase '[valuable feedback, fast](/training/valuable-feedback-fast/)': we use tools to support our testing to help us get valuable information about the state of our product in the most efficient manner possible.

The 'fast' part of 'valuable feedback, fast' is pretty self-explanatory for most people: as build and release cycles are becoming shorter, teams want to be informed timely about any unexpected changes in behaviour of their product, often after every change they make to that product.

Tools can help them achieve that by running [quick, focused tests](/breaking-down-your-e2e-tests-an-example/) automatically when a change is made or committed to version control. Of course, it takes plenty of hard work to write those tests to be fast, but that's not what I wanted to talk about here.

The 'valuable' in 'valuable feedback, fast' is a much more ambiguous term, and one that deserves some more explanation. To me, there are multiple dimensions to what makes a test valuable, and in this post, I want to unpack and address them one by one.

## Valuable = important to someone who matters
Borrowing from the classic definition of 'quality' as defined by Jerry Weinberg and further refined by James Bach and Michael Bolton, this is where it all starts. The information presented by a test should be important to someone who matters. That someone could be a member of the development team, a stakeholder such as a product owner or business analyst, the end user of the product, or a combination of those.

Without that importance, a test is meaningless, dead weight. It could be the most reliable, best-written test ever, but if the information that is provided by it is not important to someone who matters in the context of the product, why bother writing, running and maintaining the test?

## Valuable = covering what matters
Test coverage is a tricky subject, and I want to steer clear of the discussion on what 'coverage' means exactly in this blog post. The only realistic answer is 'it depends', anyway, as there are so many ways to define coverage (line, branch, requirements, mutation, ...).

Having said that, for the information provided by our tests to be valuable, teams should invest time in making sure that the tests cover the parts of the product behaviour that are deemed 'important enough' in a sufficient manner. What exactly constitutes 'sufficient' here depends on, you guessed it, the context.

Some products require deeper, more thorough coverage than others. The same applies to individual parts of the same product. It all depends on the acceptable amount of risk a team is willing to take before putting a product in the hands of their users. Teams would do well to have a continual discussion about these risks and the extent to which they are covered by the tests that accompany and scrutinize the product.

## Valuable = trustworthy
The higher the degree of automation in the build and delivery process of a product, and that includes testing, the more teams will rely (and have to rely) on the results of the execution of that automation. Concerning tests, that means that teams need to be able to rely on the information presented by the tests, because they will make decisions based on that information.

The nature of that decision might vary from anywhere between 'this build seems sufficiently stable to warrant deeper testing' to 'this change is ready to be put in the hands of our users'. No matter what the specific decision is, if teams make it based on the results of your test automation, even in part, they can only confidently do so if the information provided by the tests is trustworthy.

In practice, that means that when a test emits a signal indicating a problem with the product, the team can safely conclude that there is a problem _with the product_, not with the test, the data it uses or the environment it runs in (no false positives). It also means that when a test does not emit such a signal, the team can trust that the particular piece of behaviour exercised by the test is working according to expectations expressed in the test (no false negatives).

## Valuable = actionable
Another dimension of the value of the feedback provided by a test is that it should be actionable. This applies specifically to those situations where a test 'fails', i.e., it indicates a problem with the product I have put 'fails' between quotes here, because the test didn't fail, the product failed the test. There's a difference.

Anyway, when a test result indicates a (potential) problem with the product, teams need to able to act on that information as soon as possible, spending as little time digging deeper into the product or into the test as possible to identify the root cause of the problem. Some practices that might help here are:

* Making your test scope as small as possible - the fewer moving parts your test has, the easier it will be to identify which of those parts made a move that was unexpected
* Have good test names - A descriptive test name that tells you what part of the behaviour your product verifies and what the expected behaviour is helps in finding out where exactly the problem might be found
* Use custom assertion messages - Many test frameworks allow you to specify custom, descriptive error messages in case of assertion failures (something RestAssured.Net [supports as of version 5.0.0](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide#custom-error-messages){:target="_blank"}, too)

So, is this a complete and final definition of what 'valuable' means to me when I talk about 'valuable feedback, fast' as the goal of test automation? I don't think so. I don't know if it is complete, but it definitely is a good reflection of my current thoughts on 'value' in test automation right now. Those thoughts are definitely not 'final', and I would appreciate your takes on what I wrote here.