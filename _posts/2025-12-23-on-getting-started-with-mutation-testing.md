---
title: On getting started with mutation testing
layout: post
permalink: /on-getting-started-with-mutation-testing/
categories:
  - Mutation testing
tags:
  - artificial intelligence
  - testing your tests
  - mutation testing
---
As members of a software development team, we spend a lot of time building products that our end users (hopefully) enjoy using. We also spend a significant amount of time performing testing on these products, as well as on writing automation to support that testing. And the higher the degree of automation in the building, deployment and delivery process, the more trust we place in the results of these automated tests.

All this is nothing new: test automation, build pipelines and practices like continuous integration have been part of our ways of working for a long time now. So, why is it then that, while they place a lot of trust in our automated tests, teams (or at least those that I have been working with...) typically spend way less time on discovering information about the quality of their automated tests?

In an attempt to change this, I often joke in my talks and courses that once a newly written piece of test code compiles and runs without error, and the test produces a green checkmark, I start asking questions to myself. This cannot be right! I typically change something in my test, often the expected outcome in an assertion, and I run the test again to see if it now fails, with the expected error message in the shape of

{% highlight console %}
Expected: banana, actual: strawberry
{% endhighlight %}

or something to that extent. Doing this gives me at least a little more [confidence that my test is able to fail](/testing-your-tests/). As I like to say (and I didn't come up with this expression, but I cannot for the life of me remember whom I've heard it from):

> Never trust a test you haven't seen fail

I think that this line becomes even more relevant and important when you do not write the tests yourself, but rather you decide to hand over that part to a piece of software. This might be a model-based testing tool generating tests from a model of the software that you defined, but these days, it'll mostly be an LLM generating the tests for you. Do you really trust those tests? What is that trust based on? Do you know whether those tests can actually fail?

This is the reason I find myself talking about and working with mutation testing tools more and more often these days: because I want to know to what extent I can trust the tests I'm relying on, what kind of changes in the behaviour of my product they are able to detect and which ones they let slip by. _Especially_ when I didn't write the tests myself, but I have to work with them or rely on them in any way, I want to know what I am placing my trust in.

For those of you who haven't heard about [mutation testing](https://en.wikipedia.org/wiki/Mutation_testing){:target="_blank"} before: it is a technique where you use a mutation testing tool to deliberately inject changes into your product code, creating mutants, and then run your tests to see if they detect these changes. If a change is detected, i.e., if at least one test fails because of the change, we say that the mutant is killed, and that's a good thing: you now know that your test suite is capable of detecting that specific change in behaviour in your product code. If a mutant survives, i.e., no test fails after the change has been made, that might be a signal that your test suite needs improvement.

If you're looking for examples of mutation testing in action, here are a few blog posts I wrote before that include them:

* [Here's one](/mutation-testing-not-just-for-unit-tests/) where I explain that mutation testing is not just for unit tests
* [Here's another one](/improving-the-tests-for-rest-assured-net-with-mutation-testing-and-stryker-net/) where I show how I use mutation testing to analyze and improve the tests for RestAssured.Net

Oh, and if you would like to get some hands-on experience with mutation testing yourself, I also offer [a workshop on the topic](/training/mutation-testing/).

So, now that you know what mutation testing is and how it works, how and where do you get started? As I said, mutation testing comes up more and more frequently in conversations with fellow testers and with clients, and this is what I typically recommend:

1. Identify a small but important piece of code in your product, code that you think should be covered well by your tests. Classes that contain the implementation for important business logic are great candidates for this.
2. Identify the tests that cover this piece of code. As mentioned in the blog post I linked to earlier, this can be unit tests, integration tests or even end-to-end tests, but the slower the tests, the longer the mutation testing process takes and the longer the feedback loop will be.
3. Configure a mutation testing tool of your choice to mutate the piece of important product code you identified, and to run the tests that you want to scrutinize. Most popular mutation testing tools, including [Stryker for JavaScript](https://stryker-mutator.io/docs/stryker-js/introduction/){:target="_blank"} and [for .NET](https://stryker-mutator.io/docs/stryker-net/introduction/){:target="_blank"} and [PITest for Java](https://pitest.org/quickstart/){:target="_blank"} make this a straightforward process.
4. Analyze the test report produced by your mutation testing tools of choice and see if and where it has identified room for improvement in your test suite. If so, introduce the improvements, run mutation testing again and see if the results have improved, that is, the mutation testing coverage has increased.

Speaking of mutation testing coverage, like all other coverage metrics, it isn't a holy grail. I wouldn't recommend aiming for a very specific mutation testing coverage percentage, as that might lead to you writing tests for the sake of writing tests, instead of writing tests because they produce feedback that is important to you. What I would recommend instead, is to run mutation testing periodically and keep track of the mutation testing score. If it is improving, you're probably doing something right. If it is going down, that might require your attention.

Oh, and speaking about running mutation testing regularly, this is a type of testing that I wouldn't recommend you make part of your build pipeline, at least not initially. I say this for two reasons:

* Mutation testing is a time-consuming process, and your build pipeline should be designed to run fast and to produce valuable feedback, fast
* Analysis of mutation testing results is something that (at least to my knowledge) cannot be easily automated, and especially early on, it will produce a lot of signals that you might want to either act on or ignore, a decision that is best made by a human, not by a machine

That doesn't mean that mutation testing never has a place in a pipeline, but in the early stages of your mutation testing efforts, I would not recommend including it.

Even when you only run it periodically, outside your build pipeline, mutation testing can be a really valuable addition to your testing and automation strategy. As I said earlier in this blog post, testing your tests is important, and mutation testing is a very powerful and effective way to get more information about what kinds of changes in the behaviour of your product code your tests are able to detect. This is true for the tests that you wrote yourself, but even more so for tests that have been generated by an LLM, even if only partially.

That also makes for an apt tl;dr for this post: To trust your tests, test your tests. Especially when (parts of) those tests were generated for you. Mutation testing can help.

_P.S.: mutation testing will be one of the topics that we will cover in more depth in my brand new '[Valuable feedback, fast](/training/valuable-feedback-fast/)' course. This course combines many of the lessons I've learned about and much of the advice I have to share on succeeding with test automation, all built around a realistic case where you will help a company to implement test automation that delivers, you guessed it, valuable feedback, fast._

<small>Note: this blog post was written while listening to [
Deep Dish – Global Underground 025: Toronto (CD2)](https://www.youtube.com/watch?v=b9zK84hsqCc){:target="_blank"}</small>  