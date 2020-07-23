---
title: Do you really need that Cucumber with your Selenium?
layout: post
permalink: /do-you-really-need-that-cucumber-with-your-selenium/
categories:
  - General test automation
tags:
  - cucumber
  - readability
  - selenium webdriver
---
_Note: this blog post is NOT meant to discredit the value of Cucumber, SpecFlow and similar tools. Quite the contrary. I think these are fantastic tools, created and maintained by great people._

Somewhere last week I watched the recording of [Is Cucumber Automation Killing Your Project?](https://www.youtube.com/watch?v=7V5mii6FufQ){:target="_blank"}, a SauceLabs webinar presented by [Nikolay Advolodkin](https://www.linkedin.com/in/nikolayadvolodkin/){:target="_blank"}. In this webinar, Nikolay showed some interesting figures: 68% of the participants indicated that they don't collaborate with others to create business specs in three amigos sessions. However, 54% of the participants said they used Cucumber.

That means that there's a significant amount of participants that do use Cucumber without actively collaborating on the creation of specifications through practices like three amigos sessions, Specification by Example and Example Mapping. That's not the strong point of a tool like Cucumber, though. These tools really shine when they're used to support collaboration, as discussed in [this blog post](https://cucumber.io/blog/bdd/bdd-is-not-test-automation/){:target="_blank"} from [Aslak Hellesøy](https://www.linkedin.com/in/aslak/){:target="_blank"}, creator of and core contributor to the Cucumber project.

I must say that the above statistics don't surprise me. Many clients that I work with use Cucumber (or SpecFlow) in the same way, including my current one. Their reasoning?

<blockquote class="wp-block-quote">
  <p>
    "We want everybody in our team to understand what we're testing with our tests"
  </p>
</blockquote>

And for a long time, I supported this. I, too, thought that using Cucumber on top of your test automation code could be a good idea, even if you're not practicing Behaviour Driven Development. I've even written <a href="https://cucumber.io/blog/bdd/does-behavior-driven-development-make-sense-for-yo/" target="_blank" rel="noreferrer noopener" aria-label="an article on the Cucumber.io blog (opens in a new tab)">an article on the Cucumber.io blog</a> that says something to that extent. Yes, I've put in some pitfalls to avoid and things to consider, but I don't think that blog post covers my current point of view well enough.

That's where this blog post comes in. I've come to think that in a lot of projects where Cucumber is used solely as another layer in the automation stack, it does more harm than good. The only people that really read the Given-When-Then specifications are the people who create them (the automation engineers, most of the time), without regard for the additional time and effort it requires to implement and maintain this abstraction layer. There's no discussion, no validation, no Example Mapping, just an automation engineer writing scenarios and implementing them, because readability.

That, though, is not the point of this blog post. What I do want to show here are a couple of techniques you can employ to make your test methods read (almost) like prose, _without_ resorting to adding another abstraction layer like Cucumber.

Our application under test, once again, is <a rel="noreferrer noopener" aria-label="ParaBank (opens in a new tab)" href="http://parabank.parasoft.com/parabank/index.htm" target="_blank">ParaBank</a>, the world's least safe online bank (or rather, a demo web application from <a rel="noreferrer noopener" aria-label="Parasoft (opens in a new tab)" href="https://www.parasoft.com/" target="_blank">Parasoft</a>. In this demo application, you can perform a variety of different scenarios related to online banking, such as opening a new checking or savings account.

With Cucumber, an example scenario that describes part of the behaviour of ParaBank around opening new accounts might look something like this:

{% highlight gherkin %}
Given John is an existing ParaBank customer
And he has an existing checking account with a balance of 5000 dollars
When he opens a new savings account
Then a confirmation message containing the new account number is shown
{% endhighlight %}

Not too bad, right? It's readable, plain English, and (when you know that the initial balance is required for the deposit into the new savings account) describes the intended behaviour in a clear and unambiguous manner.

But here's the thing: unless this specification has been conjured up before the software was written, by the three amigos, using techniques like Specification by Example and Example Mapping, <span style="text-decoration: underline;">you don't need it</span>. It's perfectly possible to write test code that is nearly just as readable without the additional abstraction layer and dependency that a tool like Cucumber is.

I mean, if the automation engineer is the only person to read the specifications, why even bother creating them? This only presents a maintenance burden that a lot of projects could do without.

As an example, this is what the same test could look like without the Cucumber layer, but with some design decisions that are included for readability (an important aspect of test code, if you'd ask me) and which I'll describe in more detail below:

{% highlight java %}
private WebDriver driver;

@Before
public void initializeDatabaseAndLogin() {

    ApiHelpers.initializeDatabaseBeforeTest();

    driver = DriverHelpers.createADriverOfType(DriverType.CHROME);

    Credentials johnsCredentials = Credentials.builder().username("john").password("demo").build();

    new LoginPage(driver).
        load().
        loginUsing(johnsCredentials);
}

@Test
public void openAccount_withSufficientFunds_shouldSucceed() {

    Account aNewCheckingAccount =
        Account.builder().type(AccountType.CHECKING).build();

    Account depositingFromAccount =
        Account.builder().id(12345).build();

    new OpenAccountPage(driver).
        load().
        open(aNewCheckingAccount, depositingFromAccount);

    boolean newAccountIdIsDisplayed = new OpenAccountResultPage(driver).newAccountIdIsDisplayed();

    assertThat(newAccountIdIsDisplayed).isTrue();
}
{% endhighlight %}

Now, I don't know about you, but to me, that's almost as readable as the Cucumber scenario we've seen earlier. And remember: if we opted to use Cucumber instead, **we would have had to write the same code anyway**. So if there's no upfront communication happening around these scenarios (or in this case, I'd rather just call them tests) anyway, why bother including the Cucumber layer in the first place?

Let's look at some of the things I've implemented to make this code as readable as possible:

**Short tests**  
This is probably the most important one of them all, and that's why I mention it first. Your tests should be short, sweet and to the point. Ideally, they should check one thing only. Need specific data to be set up prior to the actual test? Try and do that using an API or directly in a database.

In this example, I'm calling a method `initializeDatabaseBeforeTest()` to reset the database to a known state via an API. There's plenty of reading material out there on why your tests should be short, so I'm not going to dive into this too deeply here.

**Model business concepts as types in your code**  
If you want to write tests that are human readable, it really helps to model business concepts that mean something to humans as object types in your code. For example, in the test above, we're creating a new account. An account, in the context of an online banking system, is an entity that has specific properties. In this case, an account has a type, a unique id and a balance:

{% highlight java %}
@Data
@Builder
@AllArgsConstructor
public class Account {

    private AccountType type;
    private int id;
    private double balance;

    public Account(){}
}
{% endhighlight %}

I'm using [Lombok](https://projectlombok.org/){:target="_blank"} here to generate getters and setters as well as a builder to allow for fluid object creation in my test method.

It's important that everybody understands and agrees on the definition of these POJOs (Plain Old Java Objects), such as the `Account` object here. This massively helps people that are not as familiar with the code as the person who wrote it to understand what's happening. Not using Cucumber doesn't absolve you from communicating with your amigos!

Another tip: if a property of a business object can only have specific values, use an enum, like we did here using `AccountType`:

{% highlight java %}
public enum AccountType {
    CHECKING,
    SAVINGS
}
{% endhighlight %}

This prevents objects and properties to accidentally being assigned a wrong value **and** it increases readability. Winner!

**Think hard about the methods exposed by your Page Objects**  
To further improve test readability, your Page Objects should (only) expose methods that have business meaning. Looking at the example above, the meat of the test happens on the `OpenAccount` page, where the new account is created. Next to a `load()` method used to navigate to the page directly (only use these for pages that you can load directly), it has an `open()` method that takes two arguments, both of type `Account`, the POJO we've seen before. The first one represents the new account, the second represents the account from which the initial deposit into the new account is made.

If you look at the page where you can open an account in the ParaBank application, you'll see that there's not much else to do than opening an account, so it makes sense to expose this action to the test methods that use the `OpenAccount` Page Object.

**Choose good names, then choose better ones**  
You've hopefully seen by now that I tried to choose the names I use in my code very carefully, so as to maximize readability. This is hard. I changed the names of my variables and methods many times when I created this example, and I feel that there's still more room for improvement.

Long variable and method names aren't inherently bad, as long as they stick to the point. That's why, for example, I chose to name the method that opens a new account on the `OpenAccount` page as `open()` instead of `openAccount()`.

From the context, it's clear that we're opening an account here. It's a method of the `OpenAccount` page, and its arguments are of type `Account`. No need to mention it again in the method name, as I did in an earlier iteration. By the way, I learned this from the [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/){:target="_blank"} book, which I think is a very valuable read for automation engineers. Lots of good stuff in there.

**Use libraries that help you with readability**  
Apart from Lombok, I also used [the AssertJ library](https://assertj.github.io/doc/){:target="_blank"} to help me write more readable assertions. So, instead of using the default JUnit `assertTrue()` method, I can now write

{% highlight java %}
assertThat(newAccountIdIsDisplayed).isTrue();
{% endhighlight %}

which I think is easier to read. AssertJ has a lot of methods that can help you write more readable assertions, and I think it's worth checking out for everybody writing Java test code.

So, all in all, I hope that the example above has shown you that it is possible to write (automation) code that is human readable without adding another layer of abstraction in the form of a tool like Cucumber or SpecFlow. [This GitHub repository](https://github.com/basdijkstra/ota-examples/tree/master/readable-selenium){:target="_blank"} contains the examples I've shown here, plus a couple more tests to show some more example of readable (Selenium) test code.

I'm sure there's still more room for improvement, and I'd love to hear your suggestions on how to further improve the readability of the test code shown here. My main point, though, is to show you that you don't need Cucumber to make your tests readable to humans.