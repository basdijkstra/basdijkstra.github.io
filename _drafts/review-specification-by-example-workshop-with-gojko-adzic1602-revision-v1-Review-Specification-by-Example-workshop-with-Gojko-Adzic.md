---
id: 1604
title: 'Review: Specification by Example workshop with Gojko Adzic'
date: 2016-09-19T20:33:54+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1602-revision-v1/
permalink: /1602-revision-v1/
---
For a consultant wanting to stay on top of their game (or at the very least hireable) it&#8217;s a good idea to take a step back from the daily work of consulting and writing every once in a while and get inspired by a conference or a course or workshop. When the <a href="http://www.wannaflex.nl/" target="_blank">WannaFlex</a> team announced they invited <a href="https://gojko.net/" target="_blank">Gojko Adzic</a> to bring his Specification by Example workshop to Amsterdam, it didn&#8217;t take me long to decide to sign up. I have come across the approach in previous projects, but found myself lacking experience and background information, and this workshop looked to be an excellent opportunity to get some of both. 

**What is Specification by Example?**  
In the booklet we received as a handout, Specification by Example (SbE) is defined as:

> A set of process patterns that facilitate change in software products to ensure the right product is delivered efficiently

In my own words, SbE is a set of techniques that help software development teams to reach a shared understanding of the software about to be built, in order to eliminate (or at least greatly reduce) the possibility of mismatched expectations leading to incorrect software or software that will not be accepted and used to satisfaction by the end user.

**Organization and setup**  
The SbE workshop is a two-day workshop where theory and practice around the creation and validation of software specifications take turns. First some theory, then a practical exercise, mostly conducted in small groups of four or five people. This group size is large enough to not come to an agreement on specifications too soon, but small enough to avoid the risk of endless discussions.

<a href="http://www.ontestautomation.com/?attachment_id=1603" rel="attachment wp-att-1603"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/09/sbe.png" alt="Specification by Example workshop announcement" width="1001" height="444" class="aligncenter size-full wp-image-1603" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/09/sbe.png 1001w, https://www.ontestautomation.com/wp-content/uploads/2016/09/sbe-300x133.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/09/sbe-768x341.png 768w" sizes="(max-width: 1001px) 100vw, 1001px" /></a>

**Day one: creating specifications**  
The first day of the workshop focused mainly on the importance of creating clear specifications and how to get to that point. For example, we were given a set of &#8216;traditional&#8217; requirements for a piece of software and 20 minutes to select a subset of the requirements and a pen and paper implementation. In other words, we simulated what could be sprint 1 of a development process, based on requirements that turned out to be vague, ambiguous and often plain incorrect. This is a learning technique that I have quickly come to appreciate now that I&#8217;ve experienced it first-hand: tell people just enough to get them started, let them make mistakes and discuss these afterwards. This is far more effective than simply showing and telling how something needs to be done!

We were also introduced to the process of how to get to good (or at least significantly better) specifications. In short (for the full story I highly recommend you take the workshop yourself if given the opportunity):

  1. Come up with examples of what the system should do. Any example will do as long as its valid, since every example tells you something about the desired behavior of the system you&#8217;re writing specifications for.
  2. Try and detect the pattern that&#8217;s behind the examples. What are the input parameters? What is the output? And so on.. This will help you see the bigger picture and get it clear what the system is supposed to do.
  3. Work on the boundary cases. This is where the interesting stuff happens, where questions mostly arise and where defects occur most often. Therefore, it&#8217;s a good idea to give them enough attention in the specification process.

**Day two: application**  
Day two started with ways to apply the specifications in practice. Patterns such as Given / When / Then were introduced and the link to test automation was made. As a test automation consultant, this was especially valuable to me. I&#8217;ve seen and worked with most of the tools that were discussed (such as Cucumber, SpecFlow and FitNesse), but the link to SbE theory was very useful information. We also spent some time analysing a number of specifications from real-world projects and seeing where they could be improved (if they weren&#8217;t beyond saving..). Very useful as well.

Unfortunately, due to other commitments, I couldn&#8217;t stay until the end of the workshop so I don&#8217;t know exactly what has been discussed in the afternoon of the second day. Still, the first day and a half provided me with lots of useful information and tips on how to apply SbE in practice and how to combine this with test automation in my projects.

**Lessons learned and recommendation**  
Too many to mention here! I&#8217;ve learned a lot in this workshop, both with regards to SbE as a technique as well as on how to facilitate a workshop. If you&#8217;re faced with SbE in your project or organization, or if you just want to know more about the technique and how it can help you to create better specifications and therefore better software, I highly recommend attending the SbE workshop. And if you like to learn from the best, you simply can&#8217;t go wrong with Gojko.

Thanks too to the people at Wannaflex for making this a very smooth and pleasant workshop.