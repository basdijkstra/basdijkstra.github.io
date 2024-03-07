---
title: It depends
layout: post
permalink: /it-depends/
categories:
  - Consulting
tags:
  - it depends
---
There's a running gag in the professional world that you can recognize a consultant from the fact they always answer questions in the same way:

> "It depends."

(sometimes followed by "if you want a more useful answer, this is my hourly rate.")

Cheap jokes aside, there is an underlying truth to this running gag. There are very, very few absolutes in software testing and software development.

You wouldn't jump to that conclusion from going through my LinkedIn feed, though. And I sort of understand that. People like speaking and thinking in absolutes. It is the easiest way to find or formulate an answer to a question.

Here are some examples:

Q: _'Who should write our tests, developers or testers?'_

A1: _'Developers. Testers can't write code, oh, and why do you even still have testers in the first place?'_

A2: _'No, testers know what good testing looks like, developers only write tests for the happy path anyway'_

or

Q: _'Should I switch to Playwright or stick to Selenium?'_

A1: _'Oh, you should definitely switch to Playwright, it's much faster and less flaky than Selenium'_

A2: _'No, stick to Selenium, Playwright is not mature enough yet, plus it's backed by Microsoft, eeewwww'_

or

Q: _'What's better, writing tests in code or using a low-code tool?'_

A1: _'You should always write tests in code, low-code tools are slow, clunky and expensive'_

A2: _'With low-code tools, everybody can write tests, therefore it's the best way to make testing a whole team effort'_

and I could go on for a while.

The problem with all these answers is, while the person giving these answers might mean well, they are completely stepping over one important matter.

Context.

And that's a problem. Because context matters. Always.

Formulating a proper answer to the example questions above should (has to!) take context into account.

* For the first question, that includes the skill sets of the testers and developers, the number of testers and developers in the team, the types of tests that are most important at the time, as well as other factors.
* For the second question, a lot depends on the effort it would take to switch from Selenium to Playwright, the size of the code base, current test execution time, amount of false positives and negatives, as well as other factors.
* For the third question, we should also consider the specific tool to use and its capabilities, the types of tests to create using the tool, the skill set of the people who are or will be using the tool, the total cost of writing tests, as well as other factors.

Without taking this context into account, all you're getting is [opinions](https://en.wiktionary.org/wiki/opinions_are_like_assholes){:target="_blank"}, personal preferences or sales pitches. Or a combination of those.

There's a reason many consultants answer questions that pose a choice between multiple options with 'it depends'. It's because they're looking to find out more about the context before they give their advice. And they should.

Don't trust consultants, or anybody whose advice you're soliciting in general, when they give you an immediate answer to this kind of question. They're probably trying to sell you something, if only their personal preference.

Please keep in mind that 'it depends' isn't just a running gag. It's because context matters.