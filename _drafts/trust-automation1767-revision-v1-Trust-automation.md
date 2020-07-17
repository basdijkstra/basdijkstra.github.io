---
id: 1770
title: Trust automation
date: 2017-01-31T05:04:06+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1767-revision-v1/
permalink: /1767-revision-v1/
---
By now, most people will be aware that the primary goal of test automation is NOT &#8216;finding bugs&#8217;. Sure, it&#8217;s nice if your automated tests catch a bug or two that would otherwise find its way into your production environment, but until artificial intelligence in test automation takes off (by which I mean REALLY takes off), testers will be way more adept at finding bugs than even the most sophisticated of automated testing solutions.

No, the added value of test automation is in something different: confidence. From the <a href="https://en.oxforddictionaries.com/definition/confidence" target="_blank">Oxford dictionary</a>:

> **Confidence**: Firm belief in the reliability, truth, or ability of someone or something.

A set of good automated tests will instill in stakeholders (including but definitely not limited to testers) the confidence that a system under test produces output or performs certain functions as previously specified (whether that&#8217;s also the intended way a system should work is a whole different discussion). Even after possibly rounds and rounds of changes, fixes, patches, refactorings and other updates.

This confidence is established by <a href="https://en.oxforddictionaries.com/definition/trust" target="_blank">trust</a>:

> **Trust**: The feeling or belief that one can have faith in or rely on someone or something.

Until recently, I wasn&#8217;t aware of the subtle difference between trust and confidence. It doesn&#8217;t really help either that both terms translate to the same word in Dutch (&#8216;vertrouwen&#8217;). Then I saw <a href="https://www.researchgate.net/post/What_is_the_difference_between_trust_and_confidence" target="_blank">this explanation</a> by <a href="http://brighton.academia.edu/MichalisPavlidis" target="_blank">Michalis Pavlidis</a>:

> Both concepts refer to expectations which may lapse into disappointments. However, trust is the means by which someone achieves confidence in something. Trust establishes confidence. The other way to achieve confidence is through control. So, you will feel confident in your friend that he won&#8217;t betray you if you trust him or/and if you control him.

Taking this back to software development, and to test automation in particular: confidence in the quality of a piece of software is achieved by control over its quality or by trust therein. As we&#8217;re talking about test automation here, more specifically the automated execution of predefined checks, I think it&#8217;s safe to say that we can forget about test automation being able to actively control the quality of the end product. That leaves the instilling of trust (with confidence as a result) in the system under test as a prime goal for test automation. It&#8217;s generally a bad idea to make automated checks the sole responsible entity for creating and maintaining this trust, by the way, but when applied correctly, they can certainly contribute a lot.

However, in order to enable your automated checks to build this trust, you need to trust the automated checks themselves as well. Simply (?) put: I wouldn&#8217;t be confident in the quality of a system if that quality was (even partially) assessed by automated checks I don&#8217;t trust.

Luckily, there are various ways to increase the trust in your automated checks. Here are three of them:

  * Run your automated checks early and often to see if they are fast, stable and don&#8217;t turn out to be high-maintenance drama queens.
  * <a href="http://www.ontestautomation.com/do-you-check-your-automated-checks/" target="_blank">Check your checks</a> to see if they still have the trust-creation power you intended them to have when you first created them. Investigate whether applying techniques such as <a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/" target="_blank">mutation testing</a> can be helpful when doing so.
  * Have your automated checks communicate both their intent (what is it that is being checked?) and their result (what was the result? what went wrong?) clearly and unambiguously to the outside world. This is something I&#8217;ll definitely investigate and write about in future posts, but it should be clear that the value of automated checks that are unclear as to their intent and their result are not providing the value they should.

Test automation is great, yet it renders itself virtually worthless if it can&#8217;t be trusted. Make sure that you trust your automation before you use it to build confidence in your system under test.