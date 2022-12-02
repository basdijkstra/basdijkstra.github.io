---
title: The four pillars of object-oriented programming - part 4 - abstraction
layout: post
permalink: /the-four-pillars-of-object-oriented-programming-part-4-abstraction/
categories:
  - object-oriented programming
tags:
  - java
  - oop
  - abstraction
---
_In this blog post series, I'll dive deeper into the four pillars (fundamental principles) of object-oriented programming:_

* _[Encapsulation](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/)_
* _[Inheritance](/the-four-pillars-of-object-oriented-programming-part-2-inheritance/)_
* _[Polymorphism](/the-four-pillars-of-object-oriented-programming-part-3-polymorphism/)_
* _Abstraction (**this post**)_

_Why? Because I think they are essential knowledge not just for developers, but definitely also for testers working with, reading or writing code. Understanding these principles helps you better understand application code, make recommendations on how to improve the structure of that code and, of course, write better automation code, too._

_The examples I give will be mostly written in Java, but throughout these blog posts, I'll mention how to implement these concepts, where possible, in C# and Python, too._

## What is abstraction?
[Abstraction](https://en.wikipedia.org/wiki/Abstraction_(computer_science)){:target="_blank"} is the creation of abstract representations of or blueprints for concrete concepts, most commonly classes.

Abstraction allows programmers to enforce a common structure for a group of related classes, either through use of interfaces or through abstract classes.

## Abstraction: an example
To get a better understanding of what abstraction looks like and what the benefits of abstraction are in object-oriented programming, let's look at an example. In [the previous post]((/the-four-pillars-of-object-oriented-programming-part-3-polymorphism/)) in this series, we ended up with a class `SavingsAccount` that inherited from a parent class `Account` but had its own implementation of specific methods.

If we take a closer look, however, at the relationship between `Account` and `SavingsAccount`, the parent-child relationship is probably not the best. A better way of modeling different types of classes in our code would be to have each type of account (checking, savings, investment, ...) be represented by its own class, without any parent-child relationships between them.

We do want to ensure, though, that all classes follow some sort of general structure, or rather, that they all contain specific properties and methods that are generic to all types of accounts. Applying abstraction can help us do exactly that.

First, we're going to take a look at how to do that using interfaces.

### Interfaces in action
Interfaces in Java can be seen as a form of contract that all classes that implement that interface should adhere to. It contains, at the minimum, a list of methods that should be present in all classes that follow (implement) the interface. Here's what an `Account` interface might look like:

{% highlight java %}
public interface Account {

    void withdraw(double amount);

    void deposit(double amount);
}
{% endhighlight %}

This interface tells us that all types of accounts should, at a minimum, implement a `withdraw()` method, as well as a `deposit()` method. The classes can also contain other methods that are not defined in the interface, but they _have to_ implement these.

A `SavingsAccount` class can now be defined implementing the `Account` interface like this:

{% highlight java %}
public class SavingsAccount implements Account {

    private final int number;
    private final double interestRate;

    private double balance;

    public SavingsAccount(int number, double interestRate) {
        this.number = number;
        this.balance = 0;
        this.interestRate = interestRate;
    }

    @Override
    public void withdraw(double amountToWithdraw) {
        if (amountToWithdraw <= this.balance) {
            this.balance -= amountToWithdraw;
        }
    }

    @Override
    public void deposit(double amount) {
        this.balance += amount;
    }
}
{% endhighlight %}

Note the use of the `implements` keyword here to denote that our `SavingsAccount` class follows the structure defined in the `Account` interface. Failing to implement the methods defined in the interface in the class results in a compile-time error.

Other classes, such as `CheckingAccount` can now also implement the `Account` interface, and we can even instantiate new objects using the interface data type:

{% highlight java %}
public static void main(String[] args) {

    // A checking account only requires you to specify an account number
    Account myCheckingAccount = new CheckingAccount(9876);

    // A savings account requires you to specify an account number and an interest rate
    Account mySavingsAccount = new SavingsAccount(1234, 0.03);
}
{% endhighlight %}

So, interfaces are a way to establish a common structure for classes. Again, you could see this as a form of a contract. But what if the implementation for different methods is the same across many or even all classes that implement the interface? Wouldn't that introduce a lot of duplicated code? Well, yes it would, but fear not, there are ways to address this.

One way to address this problem is by using _default methods_ in your interface. A default method is defined at the interface level and will automatically be available in all classes that implement the interface:

{% highlight java %}
public interface Account {

    void withdraw(double amount);

    void deposit(double amount);

    default void printBankInfo() {
        System.out.println("This account belongs to OhOhBank");
    }
}
{% endhighlight %}

{% highlight java %}
public static void main(String[] args) {

    Account account = new CheckingAccount(9876);
    account.printBankInfo();
}
{% endhighlight %}

However, this will only get you so far, as interfaces in Java _do not have state_, i.e., you can't define, access or modify properties in interfaces. If, for example, we want to define a common implementation for the `deposit()` method for all of our account types in the abstraction, we can't achieve that using interfaces. Instead, we'll have to use an _abstract class_.

### Abstract classes in action
Like interfaces, abstract classes provide a way to enforce a common structure on a group of related classes. In contrast to interfaces, however, abstract classes _can_ have state, and _can_ have method implementations that access and modify the state of an object, i.e., its properties. Here's what an abstract `Account` class, defining a common implementation for the `deposit()` method, could look like:

{% highlight java %}
public abstract class Account {

    protected double balance;
    
    abstract void withdraw(double amount);

    public void deposit(double amount) {
        this.balance += amount;
    }

    public void printBankInfo() {
        System.out.println("This account belongs to OhOhBank");
    }
}
{% endhighlight %}

And here's how our `SavingsAccount` class now extends `Account` (you extend an abstract class, you don't implement it):

{% highlight java %}
public class SavingsAccount extends Account {

    private final int number;
    private final double interestRate;

    public SavingsAccount(int number, double interestRate) {
        this.number = number;
        this.balance = 0;
        this.interestRate = interestRate;
    }

    @Override
    public void withdraw(double amountToWithdraw) {
        if (amountToWithdraw <= this.balance) {
            this.balance -= amountToWithdraw;
        }
    }
}
{% endhighlight %}

As you can see, the `SavingsAccount` no longer needs to contain an implementation of the `deposit()` method, as that is already supplied by the abstract class, but you can call the method on an object of type `SavingsAccount` without problems:

{% highlight java %}
public static void main(String[] args) {

    Account mySavingsAccount = new SavingsAccount(1234, 0.03);
    mySavingsAccount.deposit(100);
}
{% endhighlight %}

Furthermore, as the `balance` property is already defined in `Account`, `SavingsAccount` can access and use it without the need to explicitly define it once more. This all under the condition, of course, that your access modifiers allow you to do so.

## Abstraction in other languages
In C#, abstraction works much the same way as in Java, with some slight differences. The main difference is that in C#, you can define even more in an interface than you can in Java. You can define interfaces, and interfaces can contain method implementations, as in Java, but in C# (or at least in recent versions of the language), interfaces can also define and access state (again, properties).

This makes the difference between interfaces and abstract methods in C# even smaller than in Java. The biggest remaining differences are, in my opinion:

* Interfaces cannot contain constructors, whereas abstract methods can
* A class can only extend one abstract class, but it can implement multiple interfaces

These differences apply to both Java and C#.

Python does know the concept of an abstract class, which can be used to implement abstraction. There is such thing as an interface in Python. You can, technically, construct something that in some way resembles an interface, as is shown [in this article](https://realpython.com/python-interface/){:target="_blank"}, but in my opinion, it look pretty contrived and not like a proper interface as you would see in Java or C#.

## Abstraction in automation
Abstraction is the principle of object-oriented programming I find myself applying the least in my automation code, to the extent that it is hard for me to come up with an useful example of the use of it. I even think that if you find yourself using interfaces or abstract classes in your automation code, you might want to ask yourself the question whether you're not overengineering things...

A common example of people using abstraction in their automation code (but not implementing it themselves) is the `WebDriver` interface [in Selenium](https://github.com/SeleniumHQ/selenium/blob/trunk/java/src/org/openqa/selenium/WebDriver.java){:target="_blank"}. The fact that in your code, you can do

{% highlight java %}
WebDriver driver = new ChromeDriver();
{% endhighlight %}

and

{% highlight java %}
WebDriver driver = new FirefoxDriver();
{% endhighlight %}

allowing you to create tests that run against different browsers without having to juggle different driver objects is a demonstration of the power of abstraction.

I would love to see some examples that prove me wrong and that show that defining and using interfaces or abstract classes in your automation code is a good idea. If you do have examples where abstraction was really useful in your automation code, please send them my way and I'll happily change my mind.