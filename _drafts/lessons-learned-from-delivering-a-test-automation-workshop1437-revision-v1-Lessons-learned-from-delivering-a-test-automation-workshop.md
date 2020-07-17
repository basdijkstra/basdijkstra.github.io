---
id: 1447
title: Lessons learned from delivering a test automation workshop
date: 2016-05-16T14:33:08+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1437-revision-v1/
permalink: /1437-revision-v1/
---
As I&#8217;ve shared in <a href="http://www.ontestautomation.com/on-shaping-my-career-in-test-automation/" target="_blank">a recent post</a>, I am looking to move away from doing (only) billable-by-the-hour work and towards providing value-based services. One example of such a service would be the development and delivery of workshops related to the test automation craft and specific test automation tools. Last week (May 11th) I was given the opportunity to host a workshop on testing RESTful web services with <a href="http://rest-assured.io" target="_blank">REST Assured</a> before a group of around 25 members of <a href="https://www.testnet.org/testnet/home" target="_blank">TestNet</a>, one of the oldest and largest Dutch testing communities. In this post, I will share my experiences and some of the lessons I&#8217;ve learned with you.

**Contents of the workshop**  
The intention of the workshop was to give the participants an introduction to RESTful web services in general, as well as to REST Assured as a test automation tool and the features it provides to write tests for these web services. If you&#8217;d ask me to provide a table of contents for the workshop, it would look something like this:

  1. Introduction to RESTful web services (concepts and application)
  2. Introduction to REST Assured (installation, features, writing a first test)
  3. Basic header and body validations
  4. Parameterization of tests
  5. Handling various types of authentication
  6. Parameter passing
  7. Integration into build and CI processes

Steps 3 through 6 were all followed by a number of practical exercises that allowed the participants to start writing their own REST Assured tests. It wouldn&#8217;t be a workshop if the participants wouldn&#8217;t have had the chance to get their hands dirty!

<div id="attachment_1438" style="width: 1034px" class="wp-caption aligncenter">
  <a href="http://www.ontestautomation.com/?attachment_id=1438" rel="attachment wp-att-1438"><img aria-describedby="caption-attachment-1438" src="http://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_2.jpg" alt="Delivering the workshop" width="1024" height="768" class="size-full wp-image-1438" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_2.jpg 1024w, https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_2-300x225.jpg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_2-768x576.jpg 768w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
  
  <p id="caption-attachment-1438" class="wp-caption-text">
    Delivering the workshop &#8211; Picture courtesy of <a href="https://twitter.com/autotestersara" target="_blank">@autotestersara</a>
  </p>
</div>

**Lessons learned: preparing the workshop**  
Of course, there would be no workshop (or at least not a workshop I could deliver with confidence) without good bit of preparation. Although I had given a workshop on RESTful web service testing before, most of the content for this workshop was brand new. This means I needed quite some preparation time to create the slides, the exercises, a runnable demo project that contained the exercises and their solutions and the installation and configuration instructions for the participants.

From my experience with training and workshop delivery I knew that setting up participants&#8217; machines can be a real bottleneck. To try and circumvent this I wrote fairly detailed instructions on how to setup a machine for the workshop. Note that I wasn&#8217;t in the position to provide the participants with fully prepared laptops as this was a fairly open and casual conference. Also, as a freelancer, I don&#8217;t have the budget to keep a bunch of laptops in stock, nor do I particularly want to do so. In a previous workshop I tried to supply the participants with virtual machines that had everything preinstalled, but that approach did come with problems of its own. Let&#8217;s say I&#8217;ve found out the hard way that virtual machine do not behave the same on different platforms..

Some tips and takeaways concerning workshop preparation:

  * Expect at least half the participants to NOT prepare for the workshop. Possible solution: have the ones that forgot or didn&#8217;t have the time pair with those that did follow the instructions. Additional bonus: this increases interaction within the group. Note that it&#8217;s not a good idea to have them spend the first half hour (or more) of the workshop itself to complete the installation instructions. This uses up valuable workshop time and can be annoying for the participants that did prepare as requested.
  * Have enough material to fill the entire workshop, then prepare 50% more. Since it&#8217;s often hard to estimate the experience and skills of your audience, it&#8217;s a good thing to be on the safe side and have enough backup material at hand in case the participants breeze through the first part of your exercises.
  * If you&#8217;re delivering a technical workshop, be sure to have some alternatives to technological dependencies at hand. In my case, the public API we used for the exercises was down. Having alternatives ready ensures that the workshop does not come to a grinding halt at the first bump in the road.

**Lessons learned: delivering the workshop**  
After all preparation has been done, it&#8217;s showtime: time to deliver the goods to your audience. For me, this is the fun part, but also the part that&#8217;s most nerve-wracking. Especially the first couple of minutes, where you need to find your rhythm and pace as well as get to know your audience. The big lesson here is that with proper preparation, delivering your workshop should be fun. I always try and remind myself once or twice during the workshop that having fun is a big part of a successful workshop. Not only for yourself, but when you have fun, your enthusiasm will likely rub off on the participants as well.

Some tips and lessons learned concerning workshop delivery:

  * Ask questions to your audience. Of course, participants should be allowed to ask questions whenever they arise, but asking questions yourself is a great way to increase interaction.
  * Be sure to send any slides, documentation and code samples to the participants afterwards. It gives them something tangible to take home and review, as well as show to their peers or managers. Stick your name on it (subtly) so other people viewing the material know your name. Be generous and share, it will come back to you in the future. It might be a good idea to provide slides in a readonly format such as PDF, though.
  * Be sure to drink enough (water, that is). When you&#8217;re talking a lot, your mouth will start to get dry which makes it harder to talk clearly. I&#8217;ve experienced that this is somehow especially the case when talking in a language that&#8217;s not your mother tongue (for example English in my case). Strange, but true&#8230;

<div id="attachment_1439" style="width: 1034px" class="wp-caption aligncenter">
  <a href="http://www.ontestautomation.com/?attachment_id=1439" rel="attachment wp-att-1439"><img aria-describedby="caption-attachment-1439" src="http://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_1.jpg" alt="Students at work at the workshop" width="1024" height="1365" class="size-full wp-image-1439" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_1.jpg 1024w, https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_1-225x300.jpg 225w, https://www.ontestautomation.com/wp-content/uploads/2016/05/workshop_1-768x1024.jpg 768w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
  
  <p id="caption-attachment-1439" class="wp-caption-text">
    Students at work at the workshop &#8211; Picture courtesy of <a href="https://twitter.com/autotestersara" target="_blank">@autotestersara</a>
  </p>
</div>

**Feedback**  
Personally, I felt my workshop went pretty well. Even though most participants did not have a specific background in test automation, from what I&#8217;ve heard every participant took away at least some new knowledge. That defines &#8216;mission accomplished&#8217; for me. Especially nice was that some participants pointed out things that could be improved further. Getting this type of feedback has two distinct benefits:

  * You can use these suggestions to improve future workshops, obviously, but also:
  * The fact that you&#8217;re receiving these suggestions is an indicator that your audience is listening and engaged (with the amount and quality of the questions asked being another such indicator).

I hope me sharing all of the above is somehow useful to anyone involved in delivering technical workshops. Maybe it even motivates you to start looking into giving workshops and training yourself! If so, please do let me know, I love talking motivation, topics and techniques with fellow teachers. In the meantime, for all of you aspiring to get into delivering workshops and technical training yourself, I cannot recommend <a href="http://www.freelancetransformation.com/blog/how-to-leverage-your-technical-skills-to-sell-corporate-training-with-reuven-lerner" target="_blank">this episode</a> from the Freelance Transformation Podcast enough.

Oh, and in case you&#8217;re interested in me giving a test automation-related workshop in your organization, please do let me know as well! You can contact me through the <a href="http://www.ontestautomation.com/how-can-i-help-you/" target="_blank">contact page</a>.

I have some conference workshop proposals lined up, as well as some decent leads obtained at the same conference after the workshop, so here&#8217;s to hoping that there will be more lessons learned on technical workshops in the future!