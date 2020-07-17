---
id: 1634
title: Open sourcing my workshop on WireMock
date: 2016-10-12T21:35:48+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1629-revision-v1/
permalink: /1629-revision-v1/
---
_For those of you that want to jump to the good stuff directly, you can find the workshop slides, exercises and everything related <a href="https://github.com/basdijkstra/workshops/tree/master/wiremock" target="_blank">here</a>._

A couple of weeks ago I was given the opportunity to deliver another workshop before <a href="https://www.testnet.org/testnet/home" target="_blank">TestNet</a>, the Dutch software testing community, as part of their fall conference. Half a year ago, I did a similar workshop for their spring conference. That workshop was on RESTful API testing using <a href="http://rest-assured.io" target="_blank">REST Assured</a>, which I decided to <a href="http://www.ontestautomation.com/open-sourcing-my-workshop-an-experiment/" target="_blank">make open source</a> a little later on. I&#8217;ve received some positive feedback on that, so why not do it again?

This time, the conference was centered around test automation. The subject of my workshop this time was closely related to the main theme: stubbing test environment dependencies using <a href="http://wiremock.org" target="_blank">WireMock</a>.

<a href="http://www.ontestautomation.com/?attachment_id=1630" rel="attachment wp-att-1630"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/10/workshop_title_slide-1024x768.jpeg" alt="The title slide for my workshop" width="1024" height="768" class="aligncenter size-large wp-image-1630" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/10/workshop_title_slide-1024x768.jpeg 1024w, https://www.ontestautomation.com/wp-content/uploads/2016/10/workshop_title_slide-300x225.jpeg 300w, https://www.ontestautomation.com/wp-content/uploads/2016/10/workshop_title_slide-768x576.jpeg 768w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>

**Delivery**  
As with most workshops that day, mine was set in a classroom-style space. I had somewhere between 15 and 20 participants, which I think is pretty much the ideal group size for a hands-on workshop that involves writing code. Not so many that I can&#8217;t give proper attention to all questions asked, but not a group so small you start to doubt whether the upfront investment has been worth it. As those of you who have prepared and delivered workshops before, you know that preparing them takes a lot of time. Spending all those hours and then only having two people turn up, one of whom is a coworker and the other one seems a little lost, is a bit of a bummer. Fortunately, this wasn&#8217;t the case for me, at least not this time..

The levels of experience of the participants (semi-pro tip: know your audience) ranged from people having some prior experience with stub and mock development to people that had never in their life written a single line of code before. An interesting mix, to say the least!

**Workshop contents**  
As said, the main subject of the workshop was WireMock. I started out by telling people a little about the difficulties with regards to keeping test environments up and running and properly configured in these times of parallel development, Continuous Delivery and Testing and distributed applications. I then introduced WireMock, which I&#8217;ve done here on this site <a href="http://www.ontestautomation.com/getting-started-with-wiremock/" target="_blank">before</a> as well. Then came four cycles of me presenting a WireMock feature, followed by exercises where the participants could try this feature out for themselves. I chose to highlight the following WireMock features in the workshop:

  * Writing a first, basic stub
  * Request matching options
  * Fault simulation
  * Creating stateful mocks
  * </ul> 
    For each set of exercises, I prepared REST Assured tests that the participants could run to see if their stub implementation was correct. Call it test driven stub development if you like. This approach worked remarkably well, it definitely saved me a lot of time answering questions in the &#8216;is this correct?&#8217; vein. Test green = stub good, test red = stub needs improvement. It doesn&#8217;t get any clearer than that.
    
    Here&#8217;s an example of an exercise and the test that determines the correctness of the solution:
    
    <pre class="brush: java; gutter: false">public void setupStubExercise101() {

	/************************************************
	 * Create a stub that listens at path
	 * /exercise101
	 * and responds to all GET requests with HTTP status code 200
	 ************************************************/
}

@Test
public void testExercise101() {
        
    wme.setupStubExercise101();
	         
    given().
    when().
        get("http://localhost:9876/exercise101").
    then().
        assertThat().
        statusCode(200);
}</pre>
    
    **How it turned out**  
    Fine, I think. Time flew by and I didn&#8217;t experience any major faults, missing or incorrect slides or things like that. The experience I&#8217;m slowly gathering by doing this more often is starting to pay off, I think. I received almost exclusively positive feedback on the workshop, so I&#8217;m a happy guy. Also, everybody seemed to have learned at least something new and enjoyed the process too, no matter whether they had prior stubbing or even programming experience or not, and that has been the most important result of the morning to me. I&#8217;m looking forward to the next opportunity for delivering this workshop.
    
    **Having said that&#8230;**  
    All workshop contents, that&#8217;s:
    
      * The complete set of slides
      * All workshop exercises and their answers
      * Matching REST Assured tests to verify the stubs created
    
    can be found on <a href="https://github.com/basdijkstra/workshops/tree/master/wiremock" target="_blank">my GitHub page</a>. As with the previous workshop I&#8217;ve published in this manner, feel free to download, adapt, extend and then deliver the workshop to your liking. I look forward to hearing your experiences.
    
    _And in case you&#8217;re interested in following a WireMock workshop, but do not want to deliver this yourself, don&#8217;t hesitate to contact me. I&#8217;ll be happy to discuss options. Also, this workshop can easily be combined with a workshop on REST Assured for a full day of API testing and stubbing goodness._