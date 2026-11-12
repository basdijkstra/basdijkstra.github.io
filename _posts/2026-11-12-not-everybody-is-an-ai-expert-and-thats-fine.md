---
title: Not everybody is an AI expert - and that's fine
layout: post
permalink: /not-everybody-is-an-ai-expert-and-thats-fine/
categories:
  - Artificial Intelligence
tags:
  - Testing
  - Test automation
---
_This post was previously published through my [newsletter](/newsletter/) on November 9, 2026. From time to time, I will republish newsletter issues on my blog here if I think people (and search engines) might benefit from it. If you want to read everything I've written once it is posted, I recommend signing up for the newsletter._

If you're doing anything to stay on top of what is happening in the world of software testing and test automation, like

* browsing or even posting on LinkedIn, which I stopped doing for the foreseeable future, or
* reading blog posts - my preferred way of keeping up these days, and I highly recommend [Dawid Dylowicz's excellent Software Testing Weekly aggregator mailing list](https://softwaretestingweekly.com/){:target="_blank"} if you prefer longer-form reading, too

you'd be thinking that AI has consumed our entire field, and that everybody (or at least those who post or write in public) is an AI expert these days.

The reality, though, is far from that. Yes, some people use a lot of AI, even when I would only call a small fraction of them 'experts'. I definitely do not classify myself as an 'AI expert', by the way. A much larger group uses AI on a less frequent basis, and they're getting some value out of it. Sometimes more, sometimes less. That's the group I'm probably in.

And then there's a group of people who haven't really been using a lot of AI at all so far, beyond maybe the odd prompt to ChatGPT or Claude. I don't have the actual numbers, but I suspect that this group is a lot larger than you would think from browsing your feed or surfing the blogosphere. You just don't see them, because they're typically not the people who do a lot of work in public.

A few weeks ago, I started working with a small group of testers to teach them how to use AI in their daily work. This work covers both test automation as well as broader software testing tasks, as some of them contribute to automation on a daily basis, but not all of them do. I'm working with them one day per week, on average, for three months, and we're making small steps towards making AI a more prominent tool in their tool belt.

So, what are we working on right now? I'm pulling ideas from my '[Valuable Feedback, Fast with AI](/training/valuable-feedback-fast-with-ai/)' course, and so far, we've spent some time setting up Claude, going over some important concepts such as context windows, models, tokens, and (of course!) the probabilistic nature of LLMs, and what that means for the results they provide. We have gone through several initial conversations, using Claude to learn more about and review specific user stories, which was an excellent segue into using talking about MCPs and setting up a connection to both Jira and Confluence using the Atlassian MCP.

Last week, we also started experimenting with writing straightforward skills to make it easier for Claude to repeat certain actions in a structured way. These actions ranged from reviewing stories and acceptance criteria to refactoring small chunks of code in their (Robot Framework-based) test automation suite. What I loved about this is the avalanche of ideas that popped up in their heads when they saw how easy it was to use Claude. We made a short to-do list of ideas to try out for the next couple of weeks, and I'm looking forward to seeing them getting more confident around using Claude.

One thing I have noticed is that their level of comfort around using AI varies quite a bit. Some of them are naturally quite cautious, which means they'll ask 'can we really do this?' and 'should we really do this?' frequently before even submitting a single prompt. This is a good habit, I think, but we'll have to do some work to get them more comfortable with using LLMs, without losing too much of that caution. Others in the group are the opposite: I need to hold them back a little, because otherwise they'll create a beautiful mess within days, simply because they see so many opportunities to use Claude in their work. That, too, isn't inherently bad, but I do want to teach them not just how to get the most out of an LLM, but also (more so) how to use AI prudently.

Over the next couple of months, we'll dive deeper into writing better prompts, creating and structuring skills, better context management, and creating and using agents, and probably a few more things, too. I'm looking forward to seeing these people grow more comfortable around using AI in their daily work, and figuring out where AI genuinely adds value and where it doesn't in their own context.

Is there a deeper message in this newsletter? Maybe not, and I'm just happy to tell you about this project, and how it is a great example of a what I call '[corporate mentoring](/mentoring/)', a very efficient way of teaching a small group of engineers new skills, within their own context.

But maybe it's also to show you that, despite what you might see on the Internet, especially those places featuring a lot of self-congratulation (I'm looking at you, LinkedIn), not everybody out there is an AI expert. At least not yet. So, if you feel like you're 'behind' simply because you're not running at least 17 agents continuously, creating 5400 merge requests per day, know that you're not alone.

Quite far from it, I would say.