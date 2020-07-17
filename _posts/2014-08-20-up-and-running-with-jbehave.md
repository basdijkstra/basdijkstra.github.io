---
id: 556
title: 'Up and running with: JBehave'
date: 2014-08-20T09:40:37+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=556
permalink: /up-and-running-with-jbehave/
categories:
  - Test automation tools
tags:
  - behaviour-driven development
  - java
  - jbehave
  - up and running
---
_This is the fourth article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is JBehave?**  
From the [JBehave.org](http://jbehave.org) website: JBehave is a framework for Behaviour-Driven Development (BDD). BDD is an evolution of test-driven development (TDD) and acceptance-test driven design, and is intended to make these practices more accessible and intuitive to newcomers and experts alike. It shifts the vocabulary from being test-based to behaviour-based, and positions itself as a design philosophy.

Using JBehave follows these five simple steps, which we will follow in the examples provided in this article as well:

  1. Write your user story
  2. Map the steps in the user story to Java code
  3. Configure your user stories
  4. Run your JBehave tests
  5. Review the test results

**Where can I get JBehave?**  
JBehave can be downloaded from [this site](http://jbehave.org).

**How do I install and configure JBehave?**  
JBehave consists of a number of .jar files. All you need to do is to add these .jar files as a dependency to your Java project and you&#8217;re ready to start using JBehave. You can also set up and configure JBehave using Maven. See for more details the extensive JBehave reference guide [here](http://jbehave.org/reference/stable/).

**Creating a first JBehave user story and corresponding test**  
Before we start writing our first JBehave test, we need a Java class that we can test. For this purpose, I have written a very simple POJO class _Flipper_ with two variables: a String _state_ (which is either &#8216;even&#8217; or &#8216;odd&#8217;) and an Integer _value_. The source code for this class is included in the download at the end of this post. Among the methods for this class is a method _flipState_, which flips the state from &#8216;even&#8217; to &#8216;odd&#8217; (or the other way round) and increases the _value_ by 1. Useless, maybe, but it does the trick for this example!

_Step 1: Write your user story_  
A user story is written in the &#8216;given-when-then&#8217; format that is characteristic for the BDD approach. In our example, we want to make sure (i.e., test) that given a fresh instance of the _Flipper_ class, when we call the _flipState_ method, then the _value_ variable of the _Flipper_ class is assigned the value 2. See how I used the &#8216;given-when-then&#8217; strcuture to describe my test case? Translated to a user story that is JBehave-readable, our test case looks like this:

<pre class="brush: text; gutter: false; first-line: 1; highlight: []; html-script: false">Scenario: when a user flips a flipper, its value is increased by 1

Given a flipper
Given the flipper has value 1
When the user flips the flipper
Then the value of the flipper must become 2</pre>

_Step 2: Map the steps in the user story to Java code_  
One of the nicer features of JBehave is that you can run your tests as JUnit tests, which makes for easy integration with the rest of your testing and / or continuous integration framework. To do this, your test class containing the implementation of your story steps should extend the _JUnitStories_ class. JBehave uses the @Given, @When and @Then annotations to identify story steps and their nature. For example, our test class and the implementation of the first &#8216;given&#8217; step might look like this:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public class JBehaveTest extends JUnitStories {

	private Flipper flipper;

	@Given("a flipper")
	public void aFlipper() {

		flipper = new Flipper();
}</pre>

Now, whenever JBehave encounters the line &#8216;Given a flipper&#8217; in one of the stories that is executed, it will automatically execute the _aFlipper()_ method.

We can do the same for the &#8216;when&#8217; and &#8216;then&#8217; clauses of our story, using the appropriate annotations:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">@When("the user flips the flipper")
public void whenTheUserFlipsTheFlipper() {

	flipper.flipState();
}

@Then("the value of the flipper must become 2")
public void valueOfFlipperMustBecomeTwo() {

	Assert.assertEquals(2, flipper.getValue());
}</pre>

_Step 3: Configure your user stories_  
In order for our stories to be run, we need to tell JBehave where our stories are located, and which configuration should be used to execute them. For now, we are going to use the default configuration.

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">@Override
public Configuration configuration() {
	return new MostUsefulConfiguration().useStoryLoader(new LoadFromClasspath(getClass().getClassLoader())).useStoryReporterBuilder(new StoryReporterBuilder().withFormats(Format.CONSOLE));
}

@Override
public List&lt;CandidateSteps&gt; candidateSteps() {
	return new InstanceStepsFactory(configuration(), this).createCandidateSteps();
}
	
@Override
protected List&lt;String&gt; storyPaths() {
	return Arrays.asList("com/ontestautomation/jbehave/demo/test_value.story");
}

@Override
@Test
public void run() throws Throwable {
	super.run();
}</pre>

The _configuration_ method loads the default configuration for running our JBehave tests, known as the _MostUsefulConfiguration_. The _candidateSteps_ method loads the story steps defined in the current class (&#8216;this&#8217;). The _storyPaths_ method is used to define which stories are going to be executed. I included my sample story in the Eclipse project I used, but you can load story definitions from everywhere, including from an URL. Finally, the _run_ method allows us to run our class as a test.

_Step 4: Run your JBehave tests_  
Since we defined our JBehave tests as JUnit tests, running them is as easy as running regular JUnit tests. For example, in Eclipse, you can just right-click on your JBehave test class and select _Run As > JUnit Test_.

_Step 5: Review the test results_  
Again, since our JBehave tests are just another type of JUnit tests, we can review the results for our tests like we do for any other JUnit test. Again, in Eclipse, a new tab displaying our test results opens automatically after test execution:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/08/jbehave_test_results.png" alt="JBehave test results" width="478" height="177" class="aligncenter size-full wp-image-569" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/08/jbehave_test_results.png 478w, https://www.ontestautomation.com/wp-content/uploads/2014/08/jbehave_test_results-300x111.png 300w" sizes="(max-width: 478px) 100vw, 478px" />](http://www.ontestautomation.com/wp-content/uploads/2014/08/jbehave_test_results.png)  
In the Eclipse console, we can also see that our story has been processed successfully:

<pre class="brush: text; gutter: false; first-line: 1; highlight: []; html-script: false">Processing system properties {}
Using controls EmbedderControls[batch=false,skip=false,generateViewAfterStories=true,ignoreFailureInStories=false,ignoreFailureInView=false,verboseFailures=false,verboseFiltering=false,storyTimeoutInSecs=300,threads=1]

(BeforeStories)

Running story com/ontestautomation/jbehave/demo/test_value.story

(com/ontestautomation/jbehave/demo/test_value.story)
Scenario: when a user flips a flipper, its value is increased by 1
Given a flipper
Given the flipper has value 1
When the user flips the flipper
Then the value of the flipper must become 2</pre>

**Useful features**  
JBehave offers a lot of useful features, all of which are described in the online reference manual which can be found [here](http://jbehave.org/reference/stable/). Some of the most notable features are:

  * A variety of [reporting formats](http://jbehave.org/reference/stable/reporting-stories.html), including HTML, XML and text
  * The possibility to parameterize tests using [parameter injection](http://jbehave.org/reference/stable/parameter-injection.html)
  * The use of [composite steps](http://jbehave.org/reference/stable/composite-steps.html) to allow for reuse of basic steps and variation in test detail level

**Further reading**  
An Eclipse project including my _Flipper_ class implementation and the JBehave test story I&#8217;ve demonstrated above can be downloaded [here](http://www.ontestautomation.com/files/JBehaveDemo.zip).

Happy JBehaving!