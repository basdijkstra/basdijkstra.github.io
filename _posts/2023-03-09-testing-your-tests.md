---
title: Testing your tests
layout: post
permalink: /testing-your-tests/
categories:
  - Test automation
tags:
  - testing your tests
  - mutation testing
---
_This blog post was published earlier in [my weekly newsletter](/newsletter/) on January 10, 2023._

In this blog post, I'd like to talk about the practice of testing your tests, and why and how to do that.

To be 100% clear upfront: when I'm talking about tests here, I'm talking about verification points executed by tools, or what a lot of people in the software testing field call 'checks'. It's probably also a good idea to test your ideas, thoughts and processes around testing in general every once in a while, but that's not what I wanted to talk about here.

So, why should we test our tests? Well, I think it's a very good idea to do this, for a couple of reasons:

* Tests are software, and where software is written, mistakes are made. Since we want to minimize the risk associated with these mistakes, we might want to test our tests.
* The more automated the process of building, testing, deploying and delivering software is (and that's the direction a lot of teams are going in), the higher the responsibility of our tests is. Increasingly often, our tests are the only safety net (change detector) between code being written on a developer machine and that code ending up in a production environment. Therefore, it's probably a good idea to make sure that our tests detect the changes we want them to detect.

Put another way, we need to be able to trust our tests. But tests, like any other type of software, can deceive us. And with tests, that typically happens either through false positives or through false negatives.

False positives are the annoying ones, those tests that cry foul when there's nothing wrong in the software that they are testing. Rather, it's typically an issue with timing or synchronization, test data or the test environment.

But at least, false positives make themselves known by signaling a fail. False negatives, on the other hand, are what I sometimes call the 'silent killers', the tests that show a pass but let a change slip by undetected. Here's a simplified, actual code snippet example from a [mutation testing workshop](/training/mutation-testing/) I've run about a dozen times at conferences and with clients:

{% highlight java %}
public void withdraw(double amountToWithdraw) throws WithdrawException {

    if (amountToWithdraw > this.balance && this.type.equals(AccountType.SAVINGS)) {
        throw new WithdrawException("Cannot overdraw on a savings account");
    }

    // process funds withdrawal here 
}
{% endhighlight %}

And the test we have written to cover this logic is:

{% highlight java %}
@Test
public void overdrawFromSavingsAccount_shouldThrowWithdrawException() {

    Account account = new Account(AccountType.SAVINGS);   
    account.deposit(30);

    assertThrows(WithdrawException.class, () -> account.withdraw(50));
}
{% endhighlight %}

Do you see the problem here?

At some point, a developer might make a mistake (and they're human, so it's highly likely that they'll do at some point) and accidentally discard the second part of the conditional statement, making the code now look like this:

{% highlight java %}
public void withdraw(double amountToWithdraw) throws WithdrawException {

    if (amountToWithdraw > this.balance) {
        throw new WithdrawException("Cannot overdraw on a savings account");
    }

    // process funds withdrawal here
}
{% endhighlight %}

Our test, however, will keep passing, and our code coverage will still be at 100%. If this code goes live, it's suddenly no longer possible to overdraw on any type of account, potentially leading to confused customers, as well as a loss of revenue for the bank as it's no longer able to claim any overdraft interest.

This is just one example where testing our tests would have been valuable. Instead of relying on passing tests and some percentage of (code) coverage, it's probably a good idea to subject our tests to some more rigorous, well..., testing.

Here are a couple of techniques you can use to test your tests:

* Pair up with a fellow tester, automation engineer or developer. Two people see more than one, and pairing (or mobbing / ensembling) is a great way to immediately get feedback on what you do, spot and fix any potential bugs, discuss ways to approach solving problems, sharing knowledge and experiences, and much more.
* Do code reviews on your test code. If your context does not allow for synchronous collaboration, as described in the first bullet point, incorporating reviews for your tests is another way to get a second (or third, or fourth, or ...) pair of eyes on your tests, again greatly increasing the chance that unreliable tests are identified before they can do any harm.
* Periodically, go through your test suite and identify tests that are no longer reliable and/or valuable. Like your production code, test code evolves and rots over time. What is an effective and valuable test in the present might not be so effective or valuable at some point in the future. Rather than keeping it simply because you spent time writing it in the first place, update the test or delete once it starts losing its value. Don't fall for the [sunk cost fallacy](https://en.wikipedia.org/wiki/Sunk_cost#Fallacy_effect){:target="_blank"}.
* Use a technique like [mutation testing](/training/mutation-testing/) to get valuable information about the quality of your tests. Mutation testing tools can be a very effective aid in identifying gaps in your test coverage and weaknesses in your existing tests. You probably don't want to run these on every build, if only because of the execution time, but a thorough mutation testing run every now and then can do wonders for the quality of your test suite.

Do you have any other suggestions on how to test your tests, or whether or not you think this is a valuable activity? I'd love to read your views.