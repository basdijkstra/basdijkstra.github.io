---
title: Exercises from my courses - Keeping your tests simple
layout: post
permalink: /exercises-from-my-courses-keeping-your-tests-simple/
categories:
  - unit testing
tags:
  - java
  - data driven tests
  - exception handling
---
<em>To give people a better idea of the concepts I'm teaching in my [test automation training courses](/training), I'll highlight some exercises, and the lessons behind them in a series of blog posts. This is the first post in that series.</em>

In [a blog post I wrote a couple of years ago](/on-elegance/), I referred to the concept of 'elegance' in relation to test automation, and how keeping your tests simple is often the best way towards test automation that is readable and maintainable.

I've also been referring here and there to the fact that simple does not equal easy. Quite the contrary, writing simple tests is hard! To be able to write simple tests, you'll have to:

* be able to think logically about what it is that your tests need to do, and
* be able to structure that into well-written code, which should result in
* tests that are trustworthy and can fail for one reason and one reason only

Forgetting this and proceeding to write complex tests that are hard to read and even harder to maintain is relatively easy, a fact that I was reminded of once again recently while teaching a 'Java for testers' course.

This course teaches the basics of programming in Java and also provides a starter for people wanting to get into Java-based test automation. In the course, I ask people to write some unit tests for a `withdraw()` method in a class `Account` that represents a bank account:

{% highlight java %}
public class Account {

    private int balance;
    private int accountNumber;
    private AccountType accountType;

    public Account(int balance, int accountNumber, AccountType accountType) {

        this.balance = balance;
        this.accountNumber = accountNumber;
        this.accountType = accountType;
    }

    public void withdraw(int amountToWithdraw) {

        if (amountToWithdraw > this.balance && this.accountType.equals(AccountType.SAVINGS)) {
            throw new InsufficientFundsException("You cannot overdraw on a savings account!");
        }
        this.balance -= amountToWithdraw;
    }
}
{% endhighlight %}

Shortly before this exercise, we covered concepts such as conditionals (if-then-else) and exception handling using try/catch.

The first exercise I presented to the participants is to write a unit test for the case where we start with a savings account with an initial balance of 1000, then withdraw 500 and check that we end up with a resulting balance of 500:

{% highlight java %}
@Test
public void createSavingsAccountWithBalance1000_withdraw500_shouldResultInBalance500() {

    // Arrange - create a new savings account with an initial balance of 1000
    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    // Act - withdraw 500
    account.withdraw(500);

    // Assert - check that the remaining balance is equal to 500
    Assert.assertEquals(500, account.getBalance());
}
{% endhighlight %}

The next exercise is to create two other tests for some boundary cases: withdrawing 0 should result in a balance of 1000, and withdrawing 1000 should result in a remaining balance of 0:

{% highlight java %}
@Test
public void createSavingsAccountWithBalance1000_withdraw0_shouldResultInBalance1000() {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    account.withdraw(0);

    Assert.assertEquals(500, account.getBalance());
}

@Test
public void createSavingsAccountWithBalance1000_withdraw1000_shouldResultInBalance0() {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    account.withdraw(1000);

    Assert.assertEquals(0, account.getBalance());
}
{% endhighlight %}

I then point out that by adding these tests, we've introduced quite a bit of code duplication: we have three tests that exercise the same flow, just with different inputs and expected outputs. We can perform the same tests more efficiently by turning this into a data driven test, which is the task for exercise three:

{% highlight java%}
@Test
@DataProvider({
    "500, 500",
    "1000, 0",
    "0, 1000"
})
public void createSavingsAccountWithBalance1000_withdrawGivenAmount_shouldResultInExpectedBalance(int amountToWithdraw, int expectedBalanceAfterWithdrawal) {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    account.withdraw(amountToWithdraw);

    Assert.assertEquals(expectedBalanceAfterWithdrawal, account.getBalance());
}
{% endhighlight %}

Much better!

The next exercise, number four, is where things get tricky. In this exercise, I'll ask them to add another test (the phrasing is important here, as it provides clues on how to approach this problem), one that checks that when we try to withdraw 1100 from our savings account, the resulting balance is 1000, since we aren't allowed to overdraw on a savings account.

Remember that I said earlier that just before this exercise, participants learned about and practiced applying concepts like conditionals (if-then-else) and exception handling using try/catch... And that's exactly what they tried to apply while writing this test.

Just having learned about data-driven testing made them try to extend their solution from the previous step with an additional case:

Here's one solution someone came up with:

{% highlight java %}
@Test
@DataProvider({
    "500, 500",
    "999, 1",
    "1000, 0",
    "1100, 1000"
})
public void createSavingsAccountWithBalance1000_withdrawGivenAmount_shouldResultInExpectedBalance(int amountToWithdraw, int expectedBalanceAfterWithdrawal) {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    if (amountToWithdraw <= account.getBalance()) {
        account.withdraw(amountToWithdraw);
    }

    Assert.assertEquals(expectedBalanceAfterWithdrawal, account.getBalance());
}
{% endhighlight %}

This test essentially reimplements part of the business logic from the class in the test code, with all the risks associated with doing so. How do we ensure the correct implementation of those tests? Will we need to write tests for these tests?

Another solution that another participant came up with looked like this:

{% highlight java %}
@Test
@DataProvider({
    "500, 500",
    "999, 1",
    "1000, 0",
    "1100, 1000"
})
public void createSavingsAccountWithBalance1000_withdrawGivenAmount_shouldResultInExpectedBalance(int amountToWithdraw, int expectedBalanceAfterWithdrawal) {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    try {
        account.withdraw(amountToWithdraw);
    }
    catch (ArithmeticException ae) {
    }

    Assert.assertEquals(expectedBalanceAfterWithdrawal, account.getBalance());
}
{% endhighlight %}

Here, too, part of the business logic from our class under test, i.e., the part where the exception is thrown, reappears in the test code. As a result, this test can now fail for multiple reasons:

* An exception was thrown under the right conditions, but its type wasn't `ArithmeticException`, or no exception was thrown at all
* The arithmetic in the `withdraw()` method wasn't implemented correctly, leading to an incorrect resulting balance

While it's a good thing that tests can fail, it's an even better thing when tests can fail for one reason and one reason only, as I mentioned at the beginning of this post.

Additionally, if you expect an `ArithmaticException` to be thrown and the test passes, there's no way to be sure that the `ArithmeticException` actually was thrown, so there's a risk of introducing a false negative here, too.

What the participants forgot in their eagerness to solve the problem I gave them, with the tools I handed them earlier in the course, was that their tests should be as simple as possible. Instead, most of them tried to cram this extra case into their existing solution, with the results (and several variations on those) you saw above.

The lesson I try to teach here is quite the opposite. When you write your tests so that they can fail for one reason only, you'll likely end up with more tests, but also with tests that are easier to read, require less maintenance and, and this is pretty important, too, tests that are easy to diagnose when they do fail.

My proposed solution to the people in my class? Since you're exercising a different path in your code, _write another test_. That's the easiest road to simple tests.

Or, to put it differently: if you find yourself having to use an if-then-else or a try-catch construct in your tests, you're probably better off writing another test instead. 

{% highlight java %}
@Test
public void withdraw1100FromAccount_shouldThrowException() {

    Account account = new Account(1000, 12345, AccountType.SAVINGS);

    Assert.assertThrows(ArithmeticException.class, () -> account.withdraw(1100));

    Assert.assertEquals(1000, account.getBalance());
}
{% endhighlight %}

Yes, I know there are two assertions in this test, too, and that means that technically, it can fail for two different reasons, too, but at least we're not exercising more than one code path in a single, data-driven test anymore. If you can think of an even cleaner way to test this path, I'd love to hear it!

Oh, and of course a proper debrief was performed after these exercises to make sure that all participants understood the reasoning behind the exercises, and the lesson(s) to be learned from them.   