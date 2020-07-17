---
id: 1121
title: 'Model-based testing with GraphWalker and Selenium &#8211; part 1'
date: 2015-10-23T22:04:36+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1121
permalink: /model-based-testing-with-graphwalker-and-selenium-part-1/
categories:
  - Model-based testing
  - Selenium
tags:
  - graphwalker
  - java
  - model-based testing
  - selenium webdriver
  - test automation
---
In this post I&#8217;d like to make a start exploring the possibilities and drawbacks that <a href="https://en.wikipedia.org/wiki/Model-based_testing" target="_blank">model-based testing</a> (MBT) can offer to test automation in general and Selenium WebDriver in particular. I&#8217;ll be using <a href="http://graphwalker.org/" target="_blank">GraphWalker</a> as my MBT tool of choice, mostly because it&#8217;s open source, doesn&#8217;t have a steep learning curve and it&#8217;s available as a Java library, which makes integration with any Selenium project as simple as downloading the necessary .jar files and adding them to your test project.

I&#8217;ll use the login procedure for the <a href="http://parabank.parasoft.com" target="_blank">Parabank</a> demo application I&#8217;ve used so often before as an example. First, we need to model the login procedure using standard edges and nodes (which are called vertexes in Graphwalker):  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/parabank_login_model.png" alt="A model of the Parabank login procedure" width="755" height="703" class="aligncenter size-full wp-image-1122" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/parabank_login_model.png 755w, https://www.ontestautomation.com/wp-content/uploads/2015/10/parabank_login_model-300x279.png 300w" sizes="(max-width: 755px) 100vw, 755px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/parabank_login_model.png)  
I&#8217;ve used <a href="http://www.yworks.com/en/products/yfiles/yed/" target="_blank">yEd</a> to draw this model, mostly because this tool offers the option to export the model in .graphml format, which will come in very handy at a later stage. I&#8217;ll go into that in another post.

After the browser has been started and the Parabank homepage is displayed, we can perform either a successful or an unsuccessful login. Unsuccessful logins take us to an error page, which offers us the possibility to try again, again with both positive and negative results as a possibility. A successful login takes us to the Accounts Overview page, from where we can log out. The application offers us many more options once we have performed a successful login, but for now I&#8217;ll leave these out of scope.

Next, we are going to &#8216;draw&#8217;, i.e. implement this model in GraphWalker. This is pretty straightforward:

<pre class="brush: java; gutter: false">private Model createModel() {

	// Create a new, empty model
	Model model = new Model();

	// Create vertexes (nodes)
	Vertex v_Start = new Vertex().setName("v_Start");
	Vertex v_HomePage = new Vertex().setName("v_HomePage");
	Vertex v_ErrorPage = new Vertex().setName("v_ErrorPage");
	Vertex v_AccountsOverviewPage = new Vertex().setName("v_AccountsOverviewPage");

	// Create edges
	Edge e_StartBrowser = new Edge()
		.setName("e_StartBrowser")
		.setSourceVertex(v_Start)
		.setTargetVertex(v_HomePage);
	Edge e_LoginFailed = new Edge()
		.setName("e_LoginFailed")
		.setSourceVertex(v_HomePage)
		.setTargetVertex(v_ErrorPage);
	Edge e_LoginFailedAgain = new Edge()
		.setName("e_LoginFailedAgain")
		.setSourceVertex(v_ErrorPage)
		.setTargetVertex(v_ErrorPage);
	Edge e_LoginSucceeded = new Edge()
		.setName("e_LoginSucceeded")
		.setSourceVertex(v_HomePage)
		.setTargetVertex(v_AccountsOverviewPage);
	Edge e_LoginSucceededAfterFailure = new Edge()
		.setName("e_LoginSucceededAfterFailure")
		.setSourceVertex(v_ErrorPage)
		.setTargetVertex(v_AccountsOverviewPage);
	Edge e_Logout = new Edge()
		.setName("e_Logout")
		.setSourceVertex(v_AccountsOverviewPage)
		.setTargetVertex(v_HomePage);

	// Add vertexes to the model
	model.addVertex(v_Start);
	model.addVertex(v_HomePage);
	model.addVertex(v_ErrorPage);
	model.addVertex(v_AccountsOverviewPage);

	// Add edges to the model
	model.addEdge(e_StartBrowser);
	model.addEdge(e_LoginFailed);
	model.addEdge(e_LoginFailedAgain);
	model.addEdge(e_LoginSucceeded);
	model.addEdge(e_LoginSucceededAfterFailure);
	model.addEdge(e_Logout);

	return model;
}</pre>

Easy, right? The only downside is that this is quite a lot of work, especially when your models get pretty large. It also leaves (a lot of) room for improvement when it comes to maintainability. We&#8217;ll get to that in a later post as well.

Theoretically, we could have GraphWalker go through this model and explore all vertexes and edges, but in order to do meaningful work we need to link them to concrete action steps performed on Parabank. A good and clean way to do this is to consider the following:

  * Edges are like state transitions in your model, so this is where the action (typing in text boxes, clicking on links, etc.) ends up.
  * Vertexes represent states in your model, so this is where the verifications (checks) need to be performed.

For example, this is the implementation of the _e_StartBrowser_ edge:

<pre class="brush: java; gutter: false">WebDriver driver = null;

public void e_StartBrowser() {

	driver = new FirefoxDriver();
	driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

	driver.get("http://parabank.parasoft.com");
}</pre>

By giving the method the same name as the actual edge, GraphWalker knows that it needs to execute this code snippet every time the _e_StartBrowser_ node is encountered when running through the model. We can do the same for the vertexes, for example for the _v_HomePage_ vertex representing the page we land on when we perform a successful login:

<pre class="brush: java; gutter: false">public void v_HomePage() {

	Assert.assertEquals(driver.getTitle(),"ParaBank | Welcome | Online Banking");
}</pre>

For simplicity, we&#8217;ll just do a check on the page title, but you&#8217;re free to add any type of check you wish, of course.

Finally, we need to tell GraphWalker to load and run the model. I prefer using TestNG for this:

<pre class="brush: java; gutter: false">@Test
public void fullCoverageTest() {

	// Create an instance of our model
	Model model = createModel();
		
	// Build the model (make it immutable) and give it to the execution context
	this.setModel(model.build());
		
	// Tell GraphWalker to run the model in a random fashion,
	// until every vertex is visited at least once.
	// This is called the stop condition.
	this.setPathGenerator(new RandomPath(new VertexCoverage(100)));
		
	// Get the starting vertex (v_Start)
	setNextElement(model.getVertices().get(0));
		
	//Create the machine that will control the execution
	Machine machine = new SimpleMachine(this);
				
	// As long as the stop condition of the path generator is not fulfilled, hasNext will return true.
	while (machine.hasNextStep()) {
			
		//Execute the next step of the model.
		machine.getNextStep();
	}
}</pre>

The most important part in this test is the stop condition, which basically tells GraphWalker how to run the test and when to stop. In this example, I want to run the model and its corresponding Selenium actions randomly (within the restrictions implied by the model, of course) until every vertex has been hit at least once (100% vertex coverage). GraphWalker offers a lot of other stop conditions, such as running the model until:

  * Every edge has been hit at least once
  * A predefined vertex is hit
  * A predefined time period has passed (this is great for reliability tests)

A complete list of options for stop conditions can be found <a href="http://graphwalker.org/docs/path_generators_and_stop_conditions" target="_blank">here</a> in the GraphWalker documentation.

When we run this test, we can see in the console output that GraphWalker walks through our model randomly until the stop condition has been satisfied:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_console_output.png" alt="GraphWalker console output" width="835" height="701" class="aligncenter size-full wp-image-1127" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_console_output.png 835w, https://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_console_output-300x252.png 300w" sizes="(max-width: 835px) 100vw, 835px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_console_output.png)As usual when using TestNG, a nice little report has been created that tells us everything ran fine:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_results_pass.png" alt="GraphWalker TestNG report - pass" width="1025" height="357" class="aligncenter size-full wp-image-1128" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_results_pass.png 1025w, https://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_results_pass-300x104.png 300w, https://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_results_pass-1024x357.png 1024w" sizes="(max-width: 1025px) 100vw, 1025px" />](http://www.ontestautomation.com/wp-content/uploads/2015/10/graphwalker_results_pass.png)Note that unlike what I did in the example above, it&#8217;s probably a good idea to use soft asserts when you&#8217;re using TestNG together with GraphWalker. Otherwise, TestNG and therefore GraphWalker will stop executing at the first check that fails. Unless that&#8217;s exactly what you want, of course.

In this post we&#8217;ve seen a very basic introduction into the possibilities of MBT using GraphWalker and how it can be applied to Selenium tests. In upcoming tests I&#8217;ll dive deeper into the possibilities GraphWalker provides, how it can be used to generate executable models from .graphml models, and how to make the most of the tool while keeping your tests clean and maintainable.

The Eclipse project containing the code I&#8217;ve used in this post can be downloaded [here](http://www.ontestautomation.com/files/GraphWalkerSelenium_part1.zip).