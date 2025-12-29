---
title: The fundamentals of test automation revisited
layout: post
permalink: /the-fundamentals-of-test-automation-revisited/
categories:
  - Test automation
tags:
  - software testing
  - software development
  - learning
---
I'm not really one to run after all the latest trends and write or talk about 'the latest thing' in test automation. Instead, I prefer to focus on the fundamentals of our work, and the role they keep playing in everything we do, even when technology changes (and it does change, all the time).

Recently, in a conversation I had with a fellow tester, they asked me 'I hear you talk about studying the fundamentals of test automation, but what are those fundamentals? Can you give me an overview?'. As any good question does, that one made me think. I have written about what I think are the fundamentals of test automation in the past, which led to the release of [a free ebook](https://huddle.eurostarsoftwaretesting.com/resources/test-automation/a-test-automation-learning-path/){:target="_blank"}, published by the lovely folks at the EuroSTAR Huddle.

In that ebook, I listed five 'pillars of test automation', five areas that I think one should learn if they want to be successful with test automation. Those areas were:

* Software testing
* Software development
* Test automation strategy
* Test automation tools
* Test automation engineering

If you want to read what I had to say about every one of those areas, and if you're interested in some links to recommended resources, the ebook is still [available for free](https://huddle.eurostarsoftwaretesting.com/resources/test-automation/a-test-automation-learning-path/){:target="_blank}. I wrote it some five years ago now, and obviously things have changed since then. The beauty of fundamental principles, patterns and practices, though, is that they tend to change much less quickly than the tool 'du jour', and after rereading what I wrote myself I think most of it still holds.

However, in this blog post I _do_ want to review and refine some of the things that I wrote in that original ebook. It's not a complete rewrite, I don't think that's necessary, but there are some things I'd like to change or add to the original text.

_tl;dr: There are no significant structural changes in terms of the pillars I identified five years ago, but there are some changes to their contents, as well as some resources I want to add._

### Software testing

This one remains largely unchanged. Test automation, to me, is the activity of supporting software testing with tools to perform specific testing tasks more efficiently. To succeed in supporting software testing, one needs to know what good software testing looks like, so it makes sense to study this.

Unfortunately, like it was five years ago, I still see a lot of test automation content that seems to forget this. Content that demonstrates how to create as much test code or as many test cases as quickly as possible, without thinking deeply (or at all) about the quality of the testing being performed.

[I wrote about this recently](/less-but-better/), and I think we as a testing community owe it to ourselves and everybody we work with to keep in mind that no matter how fancy the tool we use, or how many tests we perform, if we don't know about the quality of the testing that we do, we're not doing a very good job.

We use tools to support our testing, because we want [valuable feedback, fast](/training/valuable-feedback-fast/). What we _do not_ want is to skip proper testing and replace it with poor, shallow, low quality pseudo-testing that happens to be fully executed by code. Knowing what good software testing looks like therefore remains of the highest importance.

One resource that should be added to the list of resources in the original ebook is the book '[Taking testing seriously](https://www.goodreads.com/book/show/201116293-taking-testing-seriously){:target="_blank"}' written by [James Bach](https://www.satisfice.com){:target="_blank"} and [Michael Bolton](https://www.developsense.com){:target="_blank"}. I've started reading it recently, haven't finished it yet, but it is an impressive book so far and I definitely recommend you reading it.

### Software development

This one, I would like to rename from 'software development' to 'programming'. Why? Because 'software development' is a much broader term than what I intended to cover, and by my definition includes activities such as software design, software delivery and, yes, software testing.

What I instead wrote about in the original ebook is the programming part of software development, and the skills it takes to write (test) code that is fit for purpose and easy to read, understand and maintain. Those aspects are still incredibly important to me, and therefore they remain on the list of topics I think everyone working in and with test automation should study.

And yes, this is true even now that we have AI as a tool to help us generate test code for us. In fact, I think the ability to recognize good code from bad code, and to know how to describe what 'good code' looks like, have only become more important because of it. Without these skills, you'll quickly 'vibe code' yourself into a corner and end up with a big ball of unreliable, hard to understand test code mud, which I assume isn't where you want to find yourself.

As for recommendations, today I would put a little less focus on some of the specific patterns I mentioned in the ebook. For example, I don't think that a complete understanding of all of [the SOLID principles](https://en.wikipedia.org/wiki/SOLID){:target="_blank"} is really all that necessary. Too often, I see discussions around these principles end up in 'dictionary wars' by purists who find it hard to admit that there's a lot of grey in between the black and white of their principles. However, if you like your code testable, I think it is useful to understand the [single responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle){:target="_blank"} and the [dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle){:target="_blank"}, as they have a direct impact on code testability.

What remains is that it is useful to be able to write code that is fit for purpose, easy to read, and, well, [elegant](/on-elegance/). Rather than recommending a resource, I would like to add a practice to the contents of the original ebook, and that is pair programming. Over the years, I have become a big proponent of working together with someone else to solve a problem, and I think it is also a very efficient way to share knowledge and approach a problem or task from multiple viewpoints.

### Test automation strategy

I can keep this one really short: knowing how to define and implement a solid, holistic test automation strategy remains as important as it was five years ago, and I don't see this changing in the foreseeable future. I'm still using the 5W1H method of asking 'why', 'what', 'where', 'when', 'who' and 'how' that I outlined in the original book, and it has been a valuable approach for the clients I have been working with, too. It's probably no surprise that my new '[Valuable feedback, fast](/training/valuable-feedback-fast/)' course heavily leans on these questions, too.

### Test automation tools

Like the previous categories, I think tools are still an important factor in your test automation learning path. The 'code-based or low-code' discussion is still relevant, too. However, there are some changes to be made in the list of tools I would recommend you check out, including:

* [Playwright](https://playwright.dev){:target="_blank"} wasn't really present when I first wrote the book, but it has since taken the open source testing tool market by storm
* SpecFlow has been decommissioned and has been replaced by [Reqnroll](https://reqnroll.net/){:target="_blank"}
* The TestProject OpenSDK (and TestProject as a whole) has been decommissioned, too

The biggest shift in this section, however, is of course the appearance of a wide range of AI-powered tools, be it those written completely from the ground up or those that add AI-powered capabilities to existing tools like Playwright or Selenium. Even these AI-powered tools, though, are just that: tools. One thing that hasn't changed is the fact that they still require the hands and (most importantly) the mind of a skilled engineer to be wielded successfully.

### Test automation engineering

I can keep it short for the final area of learning, too. Test automation engineering, i.e., topics like version control, build pipelines and all the other things that make test automation work have always been important and remain so to this day. What has changed slightly, maybe, is that more people, teams and organisations are recognizing this, at least, that's the conclusion I'm drawing from what people ask for in [my workshops and training courses](/training/).

As for the final part of the ebook, where I give my views on _how_ to learn all these things, my views on these haven't really changed, either. What I would add is, once again, the importance of working in pairs or small groups, both when performing the actual work and when learning how to do so. Often, they go hand in hand anyway.

What works really well with my clients, for example, when teams come up to me with a problem, is saying 'I don't have the answer to this right away, but how about we take a couple of hours to figure things out together?'. That is often a really, really efficient way to learn more about the actual problem and explore several different potential directions to solve it, with learning and knowledge transfer being a big part of the process.

So, all in all, I think the contents from the ebook I wrote in 2020 still hold up pretty well, and with the addendums you've seen in this blog post, I think it should be good to go for another 5-6 years. As I said at the beginning of this post, those fundamentals do not change too often, that is, after all, what makes them fundamental.

<small>This blog post was written while listening to [Parks & Wilson – Baroque In Session 2002](https://www.youtube.com/watch?v=D2avInskaeQ){:target="_blank"}</small>