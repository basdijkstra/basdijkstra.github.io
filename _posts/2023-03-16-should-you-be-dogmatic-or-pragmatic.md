---
title: Should you be dogmatic or pragmatic?
layout: post
permalink: /should-you-be-dogmatic-or-pragmatic/
categories:
  - Test automation
tags:
  - principles and practices
  - development
---
_This blog post was published earlier in [my weekly newsletter](/newsletter/) on January 17, 2023._

In this blog post, I want to discuss an issue that's under the hood of many questions I get, especially when I'm running training courses or workshops.

Here are a couple of examples of such questions:

_"Is it OK to have multiple assertions in a test?"_

_"Do we need to write our automation code in the same language as the application code?"_

_"Help, I have some duplication in my test code, should I refactor it?"_

While these seem to be very different questions, they do share a common denominator: they are all related to existing dogmas (or principles) in testing and automation:

_"Is it OK to have multiple assertions in a test?"_ -> Dogma: _"a test should have one and only one assertion / reason to fail"_

_"Do we need to write our automation code in the same language as the application code?"_ -> Dogma: _"you should write your tests in the same language as the application code"_

_"Help, I have some duplication in my test code, should I refactor it?"_ -> Dogma: _"Thy code shalt be DRY" (Don't Repeat Yourself, i.e., no duplication)_

The question behind the question, so to say, would thus be "Should I follow dogma?". As with so many things in life, the answer to this question is "it depends". (can you already tell I've been a consultant for a while now?). I hate answering "it depends" without following up on what 'it' (whatever 'it' may be) depends on, so let's dive a little deeper.

The alternative to being dogmatic, i.e., to following all the rules to the letter, is to be pragmatic, i.e., dealing with things sensibly and realistically, in a way that is based on practical rather than theoretical considerations.

So, what's better, being dogmatic or being pragmatic? Most of the times, I favour the pragmatic approach, but there is definitely such a thing as being too pragmatic, just like being too dogmatic is a thing. The key is finding the right balance.

Let's discuss some of the dangers of being either too dogmatic or too pragmatic by revisiting some of the example questions we've seen earlier.

_"Is it OK to have multiple assertions in a test?"_

Ideally, each test should only have a single assertion, or, in other words, a single reason to fail. This will make analyzing a failure much easier, and it will prevent later assertions from not being run whenever a prior assertion fails.

Being too dogmatic around this, however, might lead to a lot of duplication in test setup as you'll probably have to recreate the same initial state for multiple assertions. As an example, consider a confirmation screen showing both a success message and a summary of your order after successful completion of an order in a web shop. If you would be dogmatic about 'one test, one assertion', you'd have to create two orders, one for each check. This will probably be a lot of overhead for a relatively simple check.

On the other hand, you can also be too pragmatic about this. Creating and completing an order in your web shop probably requires navigating through different screens and filling in one or more forms with the required data. Along the way, you might want to check that:

* an article is successfully added to the shopping cart
* the shopping cart displays the correct total number of articles and price
* the configured payment options are available
* shipping costs are calculated correctly

and much more. The naive automation engineer might think that since there's a test that completes the order process in full, it might be wise to add various assertions along the way. Unfortunately, that results in a test that might fail for a large number of reasons, making it harder to put a finger on why exactly your test fails.

As another example, consider the "Help, I have some duplication in my test code, should I refactor it?" question we've seen earlier.

If you were dogmatic about this, you'd probably apply DRY (Don't Repeat Yourself) everywhere to avoid any and all duplication. And up to a certain point, that's probably a good idea. Extracting duplicate statement sequences into separate methods, creating abstraction layers (for example using the Page Object or Screenplay patterns in UI automation) will lead to code that has fewer duplications and is easier to read, write and maintain.

However, you can also go too far in applying DRY, and this is especially true when writing test code. To me, test code is a form of documentation on the behaviour of your software, and documentation should be easy to read, first and foremost. Abstracting all duplication away might negatively impact readability in favour of maintainability (and even that benefit can be debated), which I don't think is a good thing.

That's where the DAMP (Descriptive and Meaningful Phrases) principle comes in: favouring readability over removing any and all duplication. Here are two articles with examples applied to test code that I think are worth reading:

* [https://www.arhohuttunen.com/dry-damp-tests/](https://www.arhohuttunen.com/dry-damp-tests/){:target="_blank"}
* [https://enterprisecraftsmanship.com/posts/dry-damp-unit-tests/](https://enterprisecraftsmanship.com/posts/dry-damp-unit-tests/){:target="_blank"}

Of course, you can also be too pragmatic here and don't apply DAMP or DRY enough (or at all), which would result in unreadable code that's also hard to maintain. Often the result from shortcuts like copy-paste work, due to time pressure, ignorance or a lack of experience.

By the way, I've just learned that there's an acronym for that as well: WET (Write Everything Twice). Somebody got creative there...

So, wrapping things up, should you be dogmatic or pragmatic? I think the answer, again, is to find the right balance. As we've seen in the examples, you can be too dogmatic or too pragmatic, and both will lead to trouble. One more example of the adage that there is such a thing as 'too much of a good thing'.

Where that balance is? It depends ;) There's no fault in not getting it right from the start. What I do recommend is to take small steps, see what works, learn from your mistakes and find the right balance for your situation as you go. Now where did I see this before…

P.S. There's one exception I'd like to point out, one I tend to be very dogmatic about, and that's about retrying your tests. Please. Don't. Ever. [Here are my thoughts on that](/stop-sweeping-your-failing-tests-under-the-rug/).

As always, I'd love to hear your thoughts.