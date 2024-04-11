---
title: Some things to consider when implementing contract testing
layout: post
permalink: /some-things-to-consider-when-implementing-contract-testing/
categories:
  - Contract testing
tags:
  - contract testing
  - pact
  - consulting
---
When you're working on a [contract testing](/an-introduction-to-contract-testing-part-1-meet-the-players/) implementation, there are a lot of good resources out there to help you out with the hands-on part of your challenges. For example, if you're working with [Pact](https://pact.io){:target="_blank"}, there's [the documentation](https://docs.pact.io){:target="_blank"} and a great [community on Slack](https://slack.pact.io){:target="_blank"} to help you figure out the answer to many of your questions.

However, there's much more to the implementation of contract testing than just getting the tools to do what you want them to do, and these other aspects are much less often discussed. I was recently asked by a fellow tester if I had a blog post or some other kind of resource that could help them figure out how to go about starting to implement contract testing at their company, and I couldn't really find something right then and there.

That was the trigger for me to write this blog post, containing some questions I think should be asked _before_ you start throwing tools like Pact at your integration testing problems. This isn't a full-blown contract testing implementation playbook, but these are (at least if you'd ask me) important questions to ask before you start writing tests.

### Is contract testing the solution to our problem?

This is a very important question, and answering it requires formulating an answer to another question: _what is our problem in the first place?_ Implementing contract testing, especially the consumer-driven flavour, requires significant time and effort, and the changes to your testing are both technical and organizational.

Here are three conditions that I think should be met before you consider contract testing:

* You currently suffer from slow, unreliable integration and end-to-end testing efforts. If there's no problem, then why fix it?
* Your software system is built out of components that are developed in multiple teams. In my experience, contract testing doesn't add a lot of value if your entire software system is built within the same team. If component / service integration still gives you headaches in this case, you're probably better off working closer together as a team rather than relying on contract testing as a band-aid.
* You have an architecture that supports [breaking down these tests into smaller pieces](/breaking-down-your-e2e-tests-an-example/). This goes without saying, almost, but if your architecture doesn't have distributed components that are connected and exchange data via APIs, contract testing isn't going to help you.

If your context meets these conditions, then contract testing _might be_ a solution to your problem (no guarantees here, though), and it's definitely worth diving deeper into.

### Which approach to contract testing should we adopt?

There are several [approaches to contract testing](/approaches-to-contract-testing/), and some of them are probably better suited to your specific context and architecture than others. It is important, therefore, that you take some time thinking about which approach is likely to be the best fit for your situation.

Please note that it's perfectly possible to choose different approaches for different integrations. For example, it's fine to choose consumer-driven contract testing for the integration between two internal services that form part of the backbone of your system, but a bidirectional approach for an integration with a third party service (a situation where consumer-driven contract testing typically doesn't work). For yet other integrations, simple static schema validation might even be enough. Different contexts and different situations require different approaches.

### Where do we start?

So, now that you figured out you're actually suffering from challenges that contract testing could address, _and_ you've figured out the contract testing flavour(s) that likely is/are the best fit for your context, where do you start? What integration do you cover with contract tests first?

If you'd ask me, there are a couple of factors that play a role in selecting a candidate integration for a proof of concept / first step:

* Which integration is giving you headaches? I.e., which integration has suffered from integration issues in the past, or which one would cause serious problems if there were to be any integration issues? (It's almost as if testing should be risk-based, who would have figured...)
* In case you're looking to give consumer-driven contract testing a try: can you find an integration that crosses the boundary between teams, in other words, an integration where the consumer and the provider are developed in separate teams that still communicate with one another? Again, there's relatively little value to be added by contract testing if all the development is done inside a single team, because it's simply much less likely that there are communication or interpretation issues that lead to integration issues. If you must, start with an integration where both consumer and provider are developed in the same team, as this will cause less friction, but keep in mind that the added value might also be less.
* If you chose bidirectional contract testing: third-party provider such as payment providers or other SaaS services are a great candidate for a BDCT proof of concept. While it's very, very unlikely they'll participate in consumer-driven contract testing, they'll no doubt have an OpenAPI specification you can use for your BDCT efforts, so you can map these specs on your consumer contracts and see if there are any incompatibilities (thus potential problems) there.

### Who will do the work?

Another important thing to consider when you're starting to add contract tests to your testing approach is the question of 'who will write the tests?'. The fact that contract testing, especially using tools like Pact, is an approach to break down large-scale integration and end-to-end tests (typically the domain of testers) to unit-level tests (typically the domain of a developer) only complicates the matter, especially in teams where the classic chasm between testers and developers still exists.

Maybe you want to work on that first...

After you addressed that issue, you'll need to make sure that the people tasked with writing, running and maintaining contract tests have the skills to do so. They should know enough about what should be tested and where the integration risks are or might be, and also have the skills to write contract tests in a well-structured fashion.

My recommendation here is to have testers and developers work closely together when writing contract tests, as their respective experience and skill sets will complement each other nicely here.

### When are we going to run the tests?

If your tests aren't part of a pipeline, do they really exist?

Philosophical ponderings aside, you want your contract tests, both on the consumer and on the provider side, to be part of the respective pipelines, because that is the only way you can rely on having the most up-to-date information (contracts, verification results) at any given point in time.

Still, there are many, many ways to make these tests part of a pipeline, and it is simply impossible to list all possibilities here. Instead, I'm linking to [his page describing the steps to 'contract testing Nirvana'](https://docs.pact.io/pact_nirvana){:target="_blank"} here, as I think this is a great step-by-step 'maturity model' to gauge how you're doing in this area. The recommendations are written by the Pact team and therefore feature Pact terminology and tools, but the underlying principles should be universally applicable, even if you're using other tools.

### That's it!?

Well, yes and no. There are many more things to be considered when you're starting to implement contract testing (hey, I said this wasn't going to be a complete playbook!), but the above questions are very high on my list of things to consider when I'm talking to teams and organizations looking to start their contract testing journey.

If you have other questions that you feel should be on this list, let me know, and I'd be more than happy to add them here.