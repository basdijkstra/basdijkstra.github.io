---
title: The four pillars of object-oriented programming - part 2 - inheritance
layout: post
permalink: /the-four-pillars-of-object-oriented-programming-part-2-inheritance/
categories:
  - object-oriented programming
tags:
  - java
  - oop
  - inheritance
---
_In this blog post series, I'll dive deeper into the four pillars (fundamental principles) of object-oriented programming:_

* _[Encapsulation](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/)_
* _Inheritance (**this post**)_
* _Polymorphism_
* _Abstraction_

_Why? Because I think they are essential knowledge not just for developers, but definitely also for testers working with, reading or writing code. Understanding these principles helps you better understand application code, make recommendations on how to improve the structure of that code and, of course, write better automation code, too._

_The examples I give will be mostly written in Java, but throughout these blog posts, I'll mention how to implement these concepts, where possible, in C# and Python, too._

## What is inheritance?
[Inheritance](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)){:target="_blank"} is the practice of deriving classes from other classes, or, to put it in other words, creating parent-child relationships between classes.

Inheritance thus allows programmers to create a hierarchy of classes, which gives them the opportunity to better structure their code. Applying inheritance also removes the need for defining properties shared between classes more than once, which improves the maintainability of our code.

## Inheritance: an example
To better understand what inheritance can do for you, let's again take a look at the `Account` class we defined in [the previous post](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/). In the `withdraw()` method defined in that class, we made a distinction between the business rules for savings and checking accounts.

Now, consider a situation where we want to extend the functionality of this `Account` class, but only for savings accounts. For example, we want to be able to add interest to an account, but only if the type of the account is `AccountType.SAVINGS`.

We could solve this by using another if-then-else construct, just as we did in the `withdraw()` method, but wouldn't it be nicer if we could somehow find a way to make sure that adding interest is possible _only_ on savings accounts?

In other words, is there a way in which we can create a new type of account, that shares the properties of our existing `Account` class, but that has additional features that are specific to that type of account only? One of the ways to do this is by using inheritance.

### Implementing inheritance
In the code snippet below, we create a new class `SavingsAccount` that extends our existing `Account` class and adds the option to calculate interest:

{% highlight java %}
public class SavingsAccount extends Account {

    private double interestRate;

    public SavingsAccount() {

        super(AccountType.SAVINGS);
        this.interestRate = 0.03;
    }

    public void addInterest() {

        this.balance *= (1 + this.interestRate);
    }
}
{% endhighlight %}

This `SavingsAccount` class is created as a child class of the `Account` class, as indicated by the `extends` keyword. This means that `SavingsAccount` inherits all the non-private properties and methods defined in `Account`, so calling methods defined in `Account` on an object of type `SavingsAccount` is now possible:

{% highlight java %}
public static void main(String[] args) throws DepositException {

    SavingsAccount sa = new SavingsAccount();
    sa.deposit(500);
}
{% endhighlight %}

When instantiating a new object that inherits from another class, Java requires you to call an appropriate constructor of the parent class first, to make sure that all properties are initialized correctly.

This is exactly what the `super()` call does: it calls the constructor of the parent `Account` class, which in turn sets the account type and balance (see [the previous post](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/) for the `Account` constructor implementation).

In addition to all properties and methods defined in the parent class, an object of type `SavingsAccount` also has the `addInterest()` method at its disposal.

Please note here that inheriting properties is a one way process: `SavingsAccount` inherits the non-private properties and methods from `Account`, but `Account` does _not_ inherit properties and methods from `SavingsAccount`. As a result, it is not possible to call the `addInterest()` method on an object of type `Account`.

### Inheritance and access modifiers
The final aspect of inheritance we need to address here is the access modifiers used on the properties defined in the parent `Account` class. As you  can see in the implementation of the `addInterest()` method, there's a direct reference to the `balance` property defined in the parent class.

However, as this property was defined as `private`, even the `SavingsAccount` does not have access to it. Making it `public` again would expose access to the balance property to _all_ code again, which is not what we want, because that breaks the encapsulation we so carefully applied in the previous article... How can we deal with this?

Lucky for us, Java offers a way out by means of the `protected` access modifier:

{% highlight java %}
public class Account {

    private AccountType type;
    protected double balance;
{% endhighlight %}

This access modifier enables access to a property (or a method, or even an entire class) to the class itself, as well as to all child classes of that class, but not to other classes. Exactly what we are looking for! We can now directly access and modify the `balance` property from our `Account` class as well as from our `SavingsAccount` class, but not from anywhere else.

## Inheritance in other languages
All of what you've seen above in Java can be done in C# as well. Defining a parent-child relationship between classes looks like this:

{% highlight csharp %}
public class SavingsAccount : Account
{
    public double InterestRate { get; private set; }

    public SavingsAccount() : base()
    {
        InterestRate = 0.03;
    }
}
{% endhighlight %}

C# also offers the `protected` keyword to give access to properties and methods to child classes as well as the implementing class itself.

Python, too, enables us to use inheritance:

{% highlight python %}
class SavingsAccount(Account):

	def __init__(self):
	    Account.__init__(self, type=AccountType.Savings)
		self.interest_rate = 0.03
{% endhighlight %}

Unlike in Java and C#, with Python you can even do _multiple inheritance_, i.e., create a class that inherits properties and methods from multiple different classes.

Python does not provide an access modifier similar to `protected` in Java and C#. You can, however, still simulate this behaviour through [property decorators](https://www.freecodecamp.org/news/python-property-decorator/){:target="_blank"}.

## Inheritance in automation
I have seen two applications of the inheritance principle in automation that are used fairly often.

The first, building on the Page Object pattern that we briefly discussed in [the previous post](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/), is the use of base pages that contain common methods that apply to multiple page objects. These can be wrappers around the Selenium API, for example, introducing an explicit wait before calling `sendKeys()` or `click()`:

{% highlight java %}
public class BasePage {

    private WebDriver driver;
    private WebDriverWait wait;

    public BasePage(WebDriver driver) {

        this.driver = driver;
        this.wait = new WebDriverWait(this.driver, Duration.ofSeconds(10));
    }

    // Define a utility method that adds an explicit wait before trying to click on an element
    protected void click(By locator) {

        try {
            wait.until(ExpectedConditions.elementToBeClickable(locator));
            driver.findElement(locator).click();
        }
        catch (TimeoutException te) {
            Assertions.fail(String.format("Exception in click(): %s", te.getMessage()));
        }
    }
{% endhighlight %}

These can then be inherited by and used in a Page Object class:

{% highlight java %}
public class LoginPage extends BasePage {

    private final By textfieldUsername = By.name("username");
    private final By textfieldPassword = By.name("password");
    private final By buttonDoLogin = By.xpath("//input[@value='Log In']");

    public LoginPage(WebDriver driver) {

        super(driver);
    }

    public void loginAs(String username, String password) {

        sendKeys(textfieldUsername, username);
        sendKeys(textfieldPassword, password);

        // Use the click() defined in the base page
        click(buttonDoLogin);
    }
}
{% endhighlight %}

Another common application of inheritance in tests (in general, not just with user interface-driven tests) is specifying setup and teardown methods that apply to tests in several test classes in a base test class, and having all test classes inherit from this base test class:

{% highlight java %}
public class BaseTest {

    @BeforeEach
    public void before() {
	    System.out.println("Test setup goes here...");
    }

    @AfterEach
    public void after() {
	    System.out.println("Test teardown goes here...");
    }
}
{% endhighlight %}

{% highlight java %}
public class MyTest extends BaseTest {

    @Test
    public void testA() {
        System.out.println("Running the first test...");
    }

    @Test
    public void testB() {
        System.out.println("Running the second test...");
    }
}
{% endhighlight %}

The output generated when running these tests looks like this:

```
Test setup goes here...
Running the first test...
Test teardown goes here...
Test setup goes here...
Running the second test...
Test teardown goes here...
```

As you can see, the test setup and teardown methods defined in the base class are run before and after each of the tests in the test class, as we intended.

In the next blog post in this series, we'll take a closer look at the third of the four fundamental principles of object-oriented programming: polymorphism.