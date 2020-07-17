---
id: 1935
title: On including automation in your Definition of Done
date: 2017-07-25T12:20:42+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1932-autosave-v1/
permalink: /1932-autosave-v1/
---
Working with different teams in different organizations means that I&#8217;m regularly faced with the question of whether and how to include automation in the Definition of Done (DoD) that is used in Agile software development. I&#8217;m not an Agilist myself per se (I&#8217;ve seen too many teams get lost in overly long discussions on story points and sticky notes), but I DO like to help people and teams struggling with the place of automation in their sprints. As for the &#8216;whether&#8217; question: yes, I definitely think that automation should be included in any DoD. The answer to the &#8216;how&#8217; of including, a question that could be rephrased as the &#8216;what&#8217; to include, is a little more nuanced. 

For starters, I&#8217;m not too keen on rigid DoD statements like

  * All scenarios that are executed during testing and that can be automated, should be automated
  * All code should be under 100% unit test coverage
  * All automated tests should pass at least three consecutive times, except on Mondays, when they should pass four times.

OK, I haven&#8217;t actually seen that last one, but you get my point. Stories change from sprint to sprint. Impact on production code, be it new code that needs to be written, existing code that needs to be updated or refactored or old code that needs to be removed (my personal favorite) will change from story to story, from sprint to sprint. Then why keep statements regarding your automated tests as rigid as the above examples? Doesn&#8217;t make sense to me.

I&#8217;d rather see something like:

> Creation of automated tests is considered and discussed for every story and their overarching epic and applied where deemed valuable. Existing automated tests are updated where necessary, and removed if redundant.

You might be thinking &#8216;but this cannot be measured, how do we know we&#8217;re doing it right?&#8217;. That&#8217;s a very good question, and one that I do not have a definitive answer for myself, at least not yet. But I am of the opinion that knowing where to apply automation, and more importantly, where to refrain from automation, is more of an art than a science. I am open to suggestions for metrics and alternative opinions, of course, so if you&#8217;ve got something to say, please do.

Having said that, one metric that you might consider when deciding whether or not to automate a given test or set of tests is whether or not your technical debt increases or decreases. The following consideration might be a bit rough, but bear with me. I&#8217;m sort of thinking out loud here. On the one hand, given that a test is valuable, having it automated will shorten the feedback loop and decrease technical debt. However, automating a test takes time in itself and increases the size of the code base to be maintained. Choosing which tests to automate is about finding the right balance with regards to technical debt. And since the optimum will likely be different from one user story to the next, I don&#8217;t think it makes much sense to put overly generalizing statements with regards to what should be automated in a DoD. Instead, for every story, ask yourself  
<blockquo>Are we decreasing or increasing our technical debt when we automate tests for this story? What&#8217;s the optimum way of automating tests for this story?</blockquote> 

The outcome might be to create a lot of automated tests, but it might also be to not automate anything at all. Again, all depending on the story and its contents.

Another take on the question whether or not to include automated test creation in your DoD might be to discern between the different scope levels of tests:

  * Creating **unit tests** for the code that implements your user story will often be a good idea. They&#8217;re relatively cheap to write, they run fast and thereby, they&#8217;re giving you fast feedback on the quality of your code. More importantly, unit tests act as the primary safety net for future development and refactoring efforts. And I don&#8217;t know about you, but when I undertake something new, I&#8217;d like to have a safety net just in case. Much like in a circus. I&#8217;m deliberately refraining from stating that both circuses and Agile teams also tend to feature a not insignificant number of clowns, so forget I said that.
  * You&#8217;ll probably also want to automate a significant portion of your **integration tests**. These tests, for example executed at the API level, can be harder to perform manually and are relatively cheap to automate with the right tools. They&#8217;re also my personal favorite type of automated tests, because they&#8217;re at the optimum point between scope and feedback loop length. It might be harder to write integration tests when the component you&#8217;re integrating with is outside of your team&#8217;s control, or does not yet exist. In that case, simulation might need to be created, which requires additional effort that might not be perceived as directly contributing to the sprint. This should be taken into account when it comes to adding automated integration tests to your DoD.
  * Finally, there&#8217;s the **end-to-end tests**. In my opinion, adding the creation of this type of tests to your DoD should be considered very carefully. They take a lot of time to automate (even with an existing foundation), they often use the part of the application that is most likely to change in upcoming sprints (the UI), and they contribute the least to shortening the feedback loop.

The ratio between tests that can be automated and tests for which it make sense to be automated in sprint can be depicted as follows. Familiar picture?

<a href="http://www.ontestautomation.com/?attachment_id=1933" rel="attachment wp-att-1933"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/07/automation_dod.png" alt="Should you include automated tests in your Definition of Done?" width="1173" height="555" class="aligncenter size-full wp-image-1933" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/07/automation_dod.png 1173w, https://www.ontestautomation.com/wp-content/uploads/2017/07/automation_dod-300x142.png 300w, https://www.ontestautomation.com/wp-content/uploads/2017/07/automation_dod-768x363.png 768w, https://www.ontestautomation.com/wp-content/uploads/2017/07/automation_dod-1024x485.png 1024w" sizes="(max-width: 1173px) 100vw, 1173px" /></a>

Please note that like the original pyramid, this is a model, not a guideline. Feel free to apply it, alter it or forget it.

Jumping back to the &#8216;whether&#8217; of including automation in your DoD, the answer is still a &#8216;yes&#8217;. As can be concluded from what I&#8217;ve talked about here, it&#8217;s more of a &#8216;yes, automation should have been considered and applied where it provides direct value to the team for the sprint or the upcoming couple of sprints&#8217; rather than &#8216;yes, all possible scenarios that we&#8217;ve executed and that can be automated should have been automated in the sprint&#8217;. I&#8217;d love to hear how other teams have made automation a part of their DoD, so feel free to leave a comment.

And for those of you who&#8217;d like to see someone else&#8217;s take on this question, I highly recommend watching this talk by <a href="http://angiejones.tech/" target="_blank">Angie Jones</a> from the 2017 Quality Jam conference:  
<span class="embed-youtube" style="text-align:center; display: block;"></span>