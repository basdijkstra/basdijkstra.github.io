---
title: Writing tests with Claude Code - part 1 - initial results
layout: post
permalink: /writing-tests-with-claude-code-part-1-initial-results/
categories:
  - Artificial Intelligence
tags:
  - Claude Code
  - test automation
  - mutation testing
---
In [a recent post](/refactoring-the-rest-assured-net-code-with-claude-code/), I wrote about how I used Claude Code to analyze the code for [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} and then perform a refactoring action, using handwritten tests as the safety net. In that post, I wrote that I didn't want Claude to touch the tests themselves, and why. I was still curious, though, to find out for myself what Claude was capable of in terms of writing tests, and to what extent the trust that more and more people put into the tests written by LLMs is warranted.

In this blog post, I'll share with you some first steps in doing exactly that, and you'll read about my thoughts and my thought process along the way. You'll see how I create an initial suite of tests for a small Spring Boot-based API that I wrote for use in several of my [workshops](/training/), and how I assessed the results. In a follow-up blog post, I'll show you how I improved the test suite based on my observations, again using Claude Code.

### The starting point
As a starting point, I created a new repository containing the code for the API I wanted Claude to write tests for. Obviously, I removed the existing tests, and I also removed the README and the GitHub Actions build pipeline definition, as I want Claude to write tests based only on the product code itself, without being primed by other artifacts in the codebase. I left in are the dependencies used to write and run the tests, in this case REST Assured and JUnit.

After installing and [initializing Claude](https://github.com/basdijkstra/writing-tests-with-claude-code/blob/main/CLAUDE.md){:target="_blank"}, I asked it to generate tests using this prompt:

> "Add acceptance tests for the endpoints exposed by the AccountController to this project. Cover all the logic in the AccountService class. Use REST Assured as the tool to interact with the API. Use JUnit 5 as the test runner. Both libraries are already part of the project, see the pom.xml. Assert status codes and relevant response body elements as part of the tests. Extract common request properties into a RequestSpecification."

After some deliberation, Claude added a new test file to the project, containing 23 tests, all of them passing. [You can see these tests here](https://github.com/basdijkstra/writing-tests-with-claude-code/blob/main/src/test/java/com/ontestautomation/mutationbank/controllers/AccountControllerTest.java){:target="_blank"}. The code in this file is the raw output from the above prompt, I haven't changed anything.

It took Claude only a minute or two to write these tests, which definitely is a lot faster than what I could have done myself. But how good are they, really?

### A first look at the tests
Let's look at the coding style first. I'm seeing people argue that code quality is not really all that important anymore once AI will write most of our code, but I beg to differ, especially when it concerns our tests. Tests are documentation of the intended behaviour of our code, and I would say that being able to read that documentation as a human being, without too much effort, remains very important. So, is the generated code easy to read?

There's a `@BeforeEach` hook creating the `RequestSpecification` (an object in REST Assured containing shared HTTP request properties). There's a helper method to create a new account passing in the `AccountType` and a predefined balance. There's the aforementioned 23 tests that, especially at first glance, seem to verify things that are valuable.

What Claude did not do, very likely because I didn't explicitly ask for it, is adding an abstraction layer to make the code easier to read, [similar to the approach described here](/using-the-client-test-model-in-rest-assured-net/). We'll see how Claude does in this area in the next blog post, as I want to stick to assessing the quality of the initial output from Claude in this one.

And I have to say, all in all, for a first try, I'm not unhappy with what I'm seeing. Yes, there's room for improvement, but I have seen humans do far worse than this. Of course, this is only a small and simple API, but that hasn't stopped people (including myself) in the past from writing ugly test code for it.

When we look at what the tests seem to cover, at first glance it looks like they hit all the endpoints defined in the API controller, and most, if not all paths in the business logic defined in the service layer.

I should note here that I was able to fairly quickly and confidently come to these conclusions only because:

* I wrote the code for the API, so I have knowledge of the inner workings and the intent of the API, and
* I have plenty of experience writing tests for APIs and writing tests in REST Assured, so I'd like to think I know what 'good' looks like

If you don't have that prior knowledge and experience, it will be harder to draw meaningful conclusions from just looking at what Claude coughs up. And that comes with a significant risk: the risk of saying 'looks good to me' without actually understanding what you're approving and what you're putting your trust into, and then ending up with a safety net of tests full of holes.

### Testing the generated tests with mutation testing
To further increase our understanding of the value of the tests that were generated for me, let's see if these tests can fail. If they can't, the fact that we have generated 23 passing tests in two minutes flat is nothing more than an example of productivity theater.

My preferred method of finding out whether a test can actually fail is to [use a mutation testing tool](/on-getting-started-with-mutation-testing/). In this case, because we're working with Java code, I'll use [PITest](https://pitest.org){:target="_blank"} as my mutation testing tool of choice. I configured the tool to mutate all the code in the project and run all the tests, to get a complete overview of the quality of the test suite generated. Note that in a real life-sized project, you probably want to start by mutating only part of the code base and run part of the tests to get mutation testing feedback within a reasonable amount of time.

After about a minute, PITest reports back that the initial test suite achieves 95% line coverage. This looks impressive, but it doesn't really tell me anything. The much more valuable metric here is the number of mutants that were killed by the test suite. PITest reports that this is 91%, which, again is pretty good. In absolute numbers, out of 55 mutants generated by PITest, 50 were detected by the initial test suite.

From this number, two follow-up questions came up in my head immediately:

* Which mutants were missed by the tests, and what is the impact of that? What are we not covering yet?
* Could we have achieved the same amount of (line and mutation) coverage with fewer tests? In other words, do we have tests that are dead weight?

### Looking at the surviving mutants

First, let's have a look at the mutants that survived, i.e., changes in the API code that were not detected by any of the tests.

![claude_mt_http500](/images/blog/claude_mt_http500.png "Mutation testing: surviving mutants for HTTP 500 path")

To start, in the `CustomizedResponseEntityExceptionHandler`, the HTTP 500 path isn't covered in any of the tests, and that causes a surviving mutant. By design, the API returns an HTTP 500 when an `Exception` occurs that isn't a `ResourceNotFoundException` (returning an HTTP 404) or a `BadRequestException` (returning an HTTP 400). This looks like a useful path to cover in a test.

![claude_mt_http204](/images/blog/claude_mt_http204.png "Mutation testing: surviving mutants for HTTP 204 path")

Second, the API returns an HTTP 204 in response to a GET call to `/accounts` when there are no accounts in the database. That path isn't covered in any of the tests. This, too, seems like a useful path to test, because it is part of the intentional behaviour of the API.

![claude_mt_boundaries](/images/blog/claude_mt_boundaries.png "Mutation testing: surviving mutants for boundary values")

Finally, the tests that were written do not properly cover some of the boundary conditions, both in the logic that implements the business rule of 'you cannot overdraw on a savings account' and in the interest calculation logic. Once more, I would like to have these situations covered by tests.

These results, to me, indicate that mutation testing is a useful method of assessing what is tested and what isn't. It doesn't matter if you wrote the tests yourself, or you had them write by an LLM.

_Note: it definitely helped here that I can confidently and quickly perform this analysis of the signals produced by PITest, and of the quality of my tests, because I know that mutation testing as a technique exists, and because I know how it works._

More importantly, these results strengthen my belief that it is my moral obligation to closely watch the output of an LLM. I deeply value writing tests that test meaningful things and that are actually able to detect changes in product behaviour, and I don't think that should change when I have a tool writing the tests for me. If all I cared about was having _some_ tests to cover the API and declared, for example, 90% line coverage as 'good enough', I would be done by now.

However, I am not OK with this result yet, for the reasons I gave just now. In the next blog post, I want to return this feedback to Claude and see how well it does in updating the existing test suite based on my observations. I also want to see if I can add mutation testing to the test generation loop, and have Claude achieve better mutation coverage without my interfering, but that's something for another time.

For now, I'll conclude that when I ask Claude to generate tests in the way I have done, it produces pretty good results in terms of both line and mutation coverage, but that it missed certain key paths in my application code.

### Identifying dead weight in our test suite
Next, I want to find out if the test suite that was generated by Claude contains dead weight, that is, do we have any tests that do not uniquely contribute to either line or mutation coverage? To do so, I asked PITest to generate a report in XML format next to the HTML report, as (for some reason) only the XML report contains information about which specific test killed a specific mutant.

Performing this analysis required a bit of elbow grease, as I had to manually search the XML test report for occurrences of the test name for every test in the test suite. This, too, is probably a process that can be automated, but for now, I'm OK with doing this the manual way. I don't want to run the risk of hallucinations here, and besides, there are only 23 tests in the suite anyway.

My search tells me that four tests that were generated by Claude were not mentioned as a test killing a mutant in the results file. In all four cases, the reason behind this is that the exact same code path was already exercised in another test. For example, one of the generated tests performs a withdrawal on a checking account and verifies that the balance is updated accordingly:

{% highlight java %}
@Test
void withdraw_positiveAmount_fromCheckingAccount_updatesBalance() {
    long id = createAccount(AccountType.CHECKING, 500.0);

    given(requestSpec)
        .post("/{id}/withdraw/{amount}", id, 200.0)
    .then()
        .statusCode(200)
        .body("balance", equalTo(300.0f));
}
{% endhighlight %}

Another test in the suite, however, does the exact same thing, but for a savings account:

{% highlight java %}
@Test
void withdraw_positiveAmount_fromSavingsAccount_withSufficientFunds_updatesBalance() {
    long id = createAccount(AccountType.SAVINGS, 500.0);

    given(requestSpec)
        .post("/{id}/withdraw/{amount}", id, 200.0)
    .then()
        .statusCode(200)
        .body("balance", equalTo(300.0f));
}
{% endhighlight %}

Since the logic for handling withdrawals in the case of a high enough balance is exactly the same, no matter what the account type is, one test is enough to properly cover this path in the API code.

After removing these four tests from the suite and running mutation testing again, as expected, I can see that the impact on both line and mutation coverage is 0, meaning that these four tests can indeed be classified as 'dead weight'.

Why go through this exercise, you might think? You practically got those extra tests for free when Claude generated them, isn't it?

Well, sort of, but tests take time to run and to maintain, and most importantly, it takes time to ingest and process the information produced by them, especially in case of potential problems. The less the time I have to spend on that, the better. If I can safely remove tests from my test suite, and by safely, I mean without apparent adversarial impact on coverage, I would say that doing so is the prudent thing to do. 

### Conclusions

So, after completing the analysis of the results of asking Claude Code to generate tests for a new code base, what do I think? Well, while I am impressed, I think a couple of words of warning are in order.

I am positively surprised by the quality and the coverage of the initial test suite. 95% line coverage and 91% mutation coverage are good numbers, and all that coverage was generated in a few minutes, definitely a lot less time than it would have taken me to write these tests myself.

There is some room for improvement in terms of readability of the tests, but that can probably be resolved by being more specific in my prompt and / or using dedicated [Claude Code skills](https://code.claude.com/docs/en/skills){:target="_blank"}. I'll explore that in more detail and write about that soon.

Now to the words of warning. While Claude achieved a pretty decent mutation coverage, it did oversee a few critical paths in the code. Maybe I was simply 'unlucky', and another attempt with the same prompt would have given better results. I don't know, but these results do tell me not to simply accept what Claude gives me at face value.

The same applies to the tests that Claude _did_ generate. 4 out of the 23 tests generated were dead weight, which equates to 17% of the test suite. Now, n = 1, and this is a small codebase and test suite, so the numbers might be skewed, but again, if you want your test suite to be as efficient and effective as possible, these are numbers that you probably don't want to ignore.

Finally, there are of course many things that Claude did _not_ do, mainly because I didn't ask it to. An example of that would be telling me that since we're working with a banking API, it probably would be a good idea to add some form of authentication to the endpoints. There's a lot more to unpack about what Claude does and does not do, and I will probably write about that in more detail somewhere in the future, too.

First, in a follow-up blog post, I'll document the process of improving the existing test suite that Claude generated, both in terms of coverage and of coding style. I will once again be using Claude and mutation testing to do that. You can find the code for the API that was used in this blog post, as well as the initial suite of tests generated by Claude, [here](https://github.com/basdijkstra/writing-tests-with-claude-code/blob/main/src/test/java/com/ontestautomation/mutationbank/controllers/AccountControllerTest.java){:target="_blank"}.