---
title: Practicing good programming principles
layout: post
permalink: /practicing-good-programming-principles/
categories:
  - General test automation
tags:
  - programming principles
  - craftsmanship
---
_This blog post was published earlier in [my weekly newsletter](/newsletter/) on February 14, 2023._

As the CFPs for some conferences I've had my eye on for a while have opened, I'm working on a proposal for a brand new tutorial focusing on fundamental programming concepts and principles that are very useful for test automation, too. Test automation is software development, after all, so I believe this workshop could be beneficial for a lot of people.

I find myself talking about and covering these principles in my training courses more and more often, too. Where my training career started with me mostly running tool-specific workshops (REST Assured and Selenium being the most common subjects), these days I try and look beyond individuals tools and teach people more general concepts of testing, automation and programming.

For example, in many of my courses, any or all of the four fundamental principles of object-oriented programming come up at some point. I.e., when we talk about writing readable and maintainable user interface-driven automation, we invariably come to talk about encapsulation and inheritance, and often about polymorphism and abstraction, too.

(If you want to read more about these principles, have a look at the four-part blog post series [starting here](/the-four-pillars-of-object-oriented-programming-part-1-encapsulation/))

Another example: a fundamental part of covering writing tests for APIs in my courses and workshops is talking about [(de-)serialization, or transforming objects into their JSON or XML representation, or the other way around](/deserializing-json-and-xml-in-rest-assured-net/). These are not techniques that are specific to test automation, but they are incredibly powerful when used properly in your test code, and libraries that support this out of the box definitely rate high in my book.

Generally speaking, there are a lot of general (object-oriented) programming principles that are potentially very useful in test code. They help you write more effective code, and that code will, on average, be easier to read and to maintain, too.

But understanding these principles have another important benefit. A month or two ago, I ran another training course where we talked about the [SOLID principles](https://www.baeldung.com/solid-principles){:target="_blank"}. Now, I've known these existed for some time, but until that course never really had to take a closer look at them.

In preparation for the course, though, I read a lot about SOLID (I had to!) and after a while, I noticed something else: applying these principles can do wonders in making your application code more testable. This is especially true for 'single responsibility' and 'dependency inversion', but the other of the SOLID principles can potentially contribute to your code being easier to test as well.

So, in summary, I think it's important for every tester working on automation, or on improving testability of application code, to know their way around principles like the ones I mentioned so far. Beware, though, that there is such a thing as 'too much of a good thing', too.

Understanding fundamental programming principles is not the same as applying them whenever and wherever you can. An integral part of understanding principles and patterns, to me, is in knowing when not to apply them. That's why I'm a big fan of the YAGNI (You Aren't Gonna Need It) principle, in automation and in coding in general.

This principle states that you shouldn't add functionality or apply patterns and principles until they are deemed necessary. In other words, you should only think about 'what do I need to do right now to make my code better right now' and stop thinking 'ooohh, this might come in handy some day'.

This is especially true when writing one-off or otherwise short-lived automation code. With this type of automation, you can probably get away with much less polished code that is more procedural in nature and easier to write in the short term. Generally, the longer the lifespan of your code, the more you will probably benefit from applying good programming principles.

What are your thoughts on learning and understanding programming principles for automation? I'd love to hear from you.

P.S. if you know of (or even run) a conference where you think the tutorial I talked about at the start of this newsletter would be a good fit, let me know!