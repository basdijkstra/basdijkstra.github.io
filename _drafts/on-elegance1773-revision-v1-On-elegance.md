---
id: 1776
title: On elegance
date: 2017-02-07T07:49:37+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1773-revision-v1/
permalink: /1773-revision-v1/
---
For the last couple of weeks, I&#8217;ve been reading up on the works of one of the most famous Dutch computer scientists of all time: <a href="https://en.wikipedia.org/wiki/Edsger_W._Dijkstra" target="_blank">Edsger W. Dijkstra</a>. Being from the same country and sharing a last name (we&#8217;re not related as far as I know, though), of course I&#8217;ve heard of him and the importance of his work to the computer science field, but it wasn&#8217;t until I saw a <a href="http://dekennisvannu.nl/site/media/Denken-als-discipline/3659" target="_blank">short documentary on his life</a> (it is in Dutch, so probably useless to most of you) that I got interested in his work. Considering the subject of this blog, his views on software testing are of course what interests me the most, but there is more..

Most of you have probably heard what is perhaps his best known quote related to software testing:

> Program testing can be used to show the presence of bugs, but never to show their absence.

However, that&#8217;s not what I wanted to write about in this blog post. Dijkstra has left his legacy by means of a large number of manuscripts, collectively known as the EWDs (named for the fact that he signed them with his initials). For those of you that have a little spare time left, you can read all transcribed EWDs <a href="https://www.cs.utexas.edu/users/EWD/transcriptions/transcriptions.html" target="_blank">here</a>.

I was particularly triggered by a quote of Dijkstra that featured in the documentary, and which is included in <a href="https://www.cs.utexas.edu/users/EWD/transcriptions/EWD12xx/EWD1284.html" target="_blank">EWD 1284</a>:

> After more than 45 years in the field, I am still convinced that in computing, elegance is not a dispensable luxury but a quality that decides between success and failure.

While Dijkstra was referring to solutions to computer science-related problems in general, and to software development and programming structures in particular, it struck me that this quote applies quite well to test automation too.

Here&#8217;s the definition of &#8216;elegance&#8217; from the <a href="https://en.oxforddictionaries.com/definition/elegance" target="_blank">Oxford Dictionary</a>:

>   1. The quality of being graceful and stylish in appearance or manner.
>   2. The quality of being pleasingly ingenious and simple.

To me, this is what defines a &#8216;good&#8217; solution to any test automation problem: one that is stylish, ingenious and simple. Too often, I&#8217;m seeing solutions proposed (or answers to questions given) that are overly complicated, unstructured, not well-thought through or just plain horrible. And, granted, I&#8217;ve been guilty of doing the same myself, and I&#8217;m probably still doing so from time to time. But as of now, I know at least one of the characteristics a good solution should adhere to: elegance.

Dijkstra also gives two typical characteristics of elegant solutions:

> An elegant solution is shorter than most of its alternatives.

This is why I like tools such as <a href="http://rest-assured.io" target="_blank">REST Assured</a> and <a href="http://wiremock.org/" target="_blank">WireMock</a> so much: the code required to define tests c.q. mocks in these tools is short, readable and powerful: elegance at work.

> An elegant solution is constructed out of logically divided components, each of which can be easily swapped out for an alternative without influencing the rest of the solution too much.

This is a standard that every test automation solution should be held up to: if any of the components fails, is no longer maintained or is being outperformed by an alternative, how much effort does it require to swap out that component?

As a last nugget of wisdom from Dijkstra, here&#8217;s another of his quotes on elegance:

> Elegant solutions are often the most efficient.

And isn&#8217;t efficiency something we all should strive for when creating automated tests? It is definitely something I&#8217;ll try and pay more attention to in the future. Along with effectiveness, that is, something that&#8217;s maybe even more important.

However, according to my namesake, I am (and probably most of you are) failing to meet his standards in all of the projects that we&#8217;re working on. As he states:

> For those who have wondered: I don’t think object-oriented programming is a structuring paradigm that meets my standards of elegance.

Ah well..