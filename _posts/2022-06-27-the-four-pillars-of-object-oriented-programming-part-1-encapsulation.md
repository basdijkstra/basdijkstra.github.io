---
title: The four pillars of object-oriented programming - part 1 - encapsulation
layout: post
permalink: /the-four-pillars-of-object-oriented-programming-part-1-encapsulation/
categories:
  - object-oriented programming
tags:
  - java
  - oop
  - encapsulation
---
_In this blog post series, I'll dive deeper into the four pillars (fundamental principles) of object-oriented programming:_

* _Encapsulation (**this post**)_
* _[Inheritance](/the-four-pillars-of-object-oriented-programming-part-2-inheritance/)_
* _[Polymorphism](/the-four-pillars-of-object-oriented-programming-part-3-polymorphism/)_
* _[Abstraction](/the-four-pillars-of-object-oriented-programming-part-4-abstraction/)_

_Why? Because I think they are essential knowledge not just for developers, but definitely also for testers working with, reading or writing code. Understanding these principles helps you better understand application code, make recommendations on how to improve the structure of that code and, of course, write better automation code, too._

_The examples I give will be mostly written in Java, but throughout these blog posts, I'll mention how to implement these concepts, where possible, in C# and Python, too._

## What is encapsulation?
[Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)){:target="_blank"} is the practice of hiding implementation details, or the inner state, of an object and selectively exposing access to these internals through public methods. Encapsulation is generally applied to prevent outside users from directly accessing and modifying properties of an object, often for reasons of security or to prevent corruption of data.

## Encapsulation: an example
To illustrate the concept of encapsulation and its importance, let's consider a class `Account` that represents a bank account, with two properties: the account type (modeled using an enum `AccountType` that can take the values `CHECKING` and `SAVINGS`) and the account balance, modeled as a double. A very naive implementation of this class might look something like this:

{% highlight java%}
public class Account {

    public AccountType type;
    public double balance;
    
    public Account(AccountType type) {
        this.type = type;
        this.balance = 0;
    }
}
{% endhighlight %}

While straightforward, this implementation raises a couple of concerns. Most importantly, all users of this class can update the value of the balance property as they see fit when performing deposit or withdraw actions, which probably isn't the best of ideas... There is no way to guarantee that business rules are followed properly, as 'good behaviour' is in no way enforced with this implementation.

A much better way would be to apply encapsulation here. To do so, we will make the properties themselves private, restricting direct access to them to the `Account` class itself. We then expose access to the property through public methods, while also enforcing some business rules:

{% highlight java %}
public class Account {

    private AccountType type;
    private double balance;

    public Account(AccountType type) {
        this.type = type;
        this.balance = 0;
    }

    public double getBalance() {
        return this.balance;
    }
    
    public void deposit(double amount) throws DepositException {
        if (amount < 0) {
            throw new DepositException("You cannot deposit a negative amount!");
        }
        this.balance += amount;
    }
    
    public void withdraw(double amount) throws WithdrawException {
        if (amount < 0) {
            throw new WithdrawException("You cannot withdraw a negative amount!");
        }
        if (amount > this.balance && this.type.equals(AccountType.SAVINGS)) {
            throw new WithdrawException("You cannot overdraw on a savings account!");
        }
        this.balance -= amount;
    }
}
{% endhighlight %}

Using encapsulation and business rule enforcement, it's now impossible for negative amounts to be withdrawn from or deposited to a bank account. It is also impossible to overdraw on a savings account. The current balance is still publicly accessible, but the access is now read-only.

This will significantly reduce the amount of unwanted behaviour in the system. However, it's a good idea to write some tests for this class anyway!

## Encapsulation in other languages
In C#, you can do something similar to what we've seen above in Java. As an example, I would probably initialize the `Balance` property like this:

{% highlight csharp %}
public double Balance { get; private set; }
{% endhighlight %}

This gives the outside world read-only access to the `balance` property itself, while it can only be updated from inside the `Account` class itself. Like in the Java example, we can then expose access to the balance through public `Deposit(double amount)` and `Withdraw(double amount)` methods that enforce the required business logic.

Python uses a different philosophy when it comes to encapsulation. By default, everything in Python is public, which means that encapsulation cannot be strictly enforced. There are some conventions that allow you to signal that properties should be handled carefully, though.

Prefixing a property with an underscore signals that the property shouldn't be accessed, let alone modified, from outside the class. Again, this is a convention, not something that is enforced, so it _is_ possible to do so.

{% highlight python %}
class Account:
    def __init__(self, type):
        self._type = type
        self._balance = 0
{% endhighlight %}

To really, really signal that a property is private, you can prefix it with a double underscore (__). While this makes the property invisible to the users of the class (you cannot directly reference it through `account.balance`, for example), it still doesn't prevent outside code from accessing it, because there's always [Python name mangling](https://www.geeksforgeeks.org/name-mangling-in-python/){:target="_blank"}, i.e., a property `__balance` of an object `account` of type `Account` can still be accessed through `account._Account__balance`.

## Encapsulation in automation
Arguably the most well-known and widely used application of the encapsulation principle in automation are Page Objects, commonly used in user interface-driven automation using tools like Selenium WebDriver, Playwright and Cypress.

Page Objects are classes that encapsulate the implementation details of a page, most notably the locators used to identify elements on a page that are used in tests. Page Objects then expose access to those methods through public methods. In doing so, they often enforce 'business logic' in the form of the sequence in which interaction with objects takes place.

Here's an example of a `LoginPage` object (using Selenium WebDriver) that hides the implementation details (the element locators) in private properties and exposes access to them through common test actions defined in public methods (in this case, logging in to an application using the `loginAs()` method):

{% highlight java %}
public class LoginPage {

    private final By textfieldUsername = By.name("username");
    private final By textfieldPassword = By.name("password");
    private final By buttonDoLogin = By.xpath("//input[@value='Log In']");

    public LoginPage(WebDriver driver) {

        driver.get("https://parabank.parasoft.com/parabank");
    }

    public void loginAs(String username, String password) {

        sendKeys(textfieldUsername, username);
        sendKeys(textfieldPassword, password);
        click(buttonDoLogin);
    }
}
{% endhighlight %}

The 'user' of such a Page Object class (typically a test method) can then interact with the elements defined in the class only through a public method, without having direct access to (and without the need to concern itself with) the page implementation details, such as the HTML structure and any synchronization with the state of the elements. All this makes for a clean API for the page object class and better separation of concerns.

In the next blog post, I'll dive deeper into the second of the four fundamental principles of object-oriented programming: inheritance.