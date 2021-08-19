---
title: On codeless automation (or rather on abstraction layers)
layout: post
permalink: /on-codeless-automation-or-rather-on-abstraction-layers/
categories:
  - general test automation
tags:
  - codeless test automation
  - abstraction layers
  - software development
---
So, about a month ago I received a message from a LinkedIn connection with a link to [a Forbes article](https://www.forbes.com/sites/forbestechcouncil/2021/06/02/the-next-wave-of-low-codeno-code-codeless-test-automation/){:target="_blank"} and their question:

> Do you think codeless is a good thing or no?

I sent them an elaborate answer with my views on the article and the 'codeless' (you'll see why I'm using quotes here later on) automation phenomenon. For some reason, they've not yet responded to my views, and to prevent my efforts from going to waste, I decided to turn my answer into the blog post you're reading right now.

So, do I think codeless is a good thing, or no?

Well, yes and no. More no than yes, though. Let me explain.

I've got absolutely no problem with tools that provide people with an additional abstraction layer on top of the statements, and ultimately the bits and bytes, that make up the tests, which is what low code tools do: provide an abstraction layer. In my current project, we're using such a tool a lot, often with good results.

Abstraction layers, in general, have their benefits and drawbacks. They make it easier to use specific parts of your code by hiding implementation details and providing (hopefully) a user-friendly API. This API can come in different forms, for example:

* It can be code, again. [REST Assured](https://rest-assured.io/){:target="_blank"}, for example, provides an abstraction layer on top of Java HTTP libraries to make it easier to write API-level tests in Java.
* It can be some other textual form. [Robot Framework](https://robotframework.org/){:target="_blank"}, for example, allows engineers to write tests using keywords in a tabular format.
* It can be a graphical user interface. [Postman](https://www.postman.com/){:target="_blank"}, for example, provides a graphical user interface that allows people to write tests by using a wide range of building blocks that you can control through the Postman GUI.

However, no matter the form of the abstraction layer, there will always be code underneath. Sometimes you write that lower-level code yourself, but often it's written for you, so you don't have to do it yourself anymore.

The biggest drawback, in my opinion, is that these abstraction layers come with a loss of flexibility. Because you can't control the code underneath, you're often stuck with what the abstraction layers in the libraries or tools you use provide you. Sometimes that's OK, sometimes that'll hold you back, either right away or at some point in the future. As always: 'it depends'.

In general, I don't have problems with abstraction layers, and as such, with low code automation tools like Robot Framework or Postman. They're often useful, as long as the people use them recognize these tools for what they are: abstraction layers, and recognize that underneath, it's still code.

And this is where my problem with 'codeless' (or 'no code', as it is also called) comes in.

While 'codeless' is undoubtedly a wonderful marketing term, it promises things that it just cannot live up to. No matter if you're using a low code tool or not, you're still writing software, just with a different syntax, provided by an abstraction layer.

That means that even if you write something that doesn't look like Java, Python, C# or whatever your language of choice is, you'll still need development skills (the ability to model, to create additional abstraction layers, to create reusable building blocks, etc.).

Unless you are aware of that and actively apply good programming principles, at some point you will paint yourself into a corner with a lot of hard to maintain automated gibberish, code or low code.

People do not realize this enough, and statements such as this one in the article I linked to at the start of this post:

> The idea is that anyone at an organization — from engineering and product to marketing, finance, legal and sales — can create automated tests quickly and easily without writing a single line of code, and all without any programming or automation expertise.

contributes to that. It simply does not work that way. If you create software, be it application software or automated scripts, you're a developer. You'll need development skills. The syntax with low code tools might be different, but the fundamental skills (thinking in an object-oriented matter, the ability to translate processes to code, etc.) remain the same.

Summarizing, I'm not against low code tools, but you should always keep in mind that you're writing software, no matter what the syntax is. This requires skill and expertise, something that comes with deliberate practice. Yes, everybody can do it, after they've put in the work to obtain the skill and expertise, but somehow I doubt that's what the marketing departments of many of these 'codeless' tools have in mind.

_Final note: An additional problem is that a lot of low code (again, I'm deliberately avoiding the term 'codeless', there simply is no such thing) tools often only work in a 'full stack' fashion operating on the graphical user interface of an application. This means that scripts in these tools can often only be created *after* the entire application has finished development. That's often too late._

_Also, this type of scripts is notoriously slow in execution in comparison to automation at lower levels (unit tests, API-level integration tests, etc.), which severely limits the benefit of faster feedback you should get from a well-designed test automation solution._