---
id: 1408
title: Data driven JavaScript tests using Jasmine
date: 2016-04-21T19:57:20+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1404-revision-v1/
permalink: /1404-revision-v1/
---
In my previous post I introduced Jasmine as a tool for testing JavaScript code. Now that I&#8217;ve had some more time to play around with it, combining it with <a href="https://angular.github.io/protractor/#/" target="_blank">Protractor</a> for user interface-driven tests for one of my client&#8217;s websites, I&#8217;ve noticed that one feature was sorely missing: built-in support for data driven testing. All BDD tools (or tools that say they support BDD) that I have worked with so far, such as <a href="http://www.ontestautomation.com/writing-bdd-tests-using-selenium-and-cucumber/" target="_blank">Cucumber</a>, <a href="http://www.specflow.org/" target="_blank">SpecFlow</a> and <a href="http://www.ontestautomation.com/getting-started-with-jgiven/" target="_blank">JGiven</a>, support the creation of data driven scenarios. However, Jasmine doesn&#8217;t, at least not as far as I have seen in the limited time I have been using it so far.

As I think data driven testing is key when creating maintainable and reusable scenarios, I fired up Google to check whether someone had already found a solution. From what I&#8217;ve read, there are (at least) two different ways to do data-driven testing with Jasmine. Let&#8217;s have a look at both.

**Specifying your test data in an array variable**  
I found the first approach in the comments of <a href="http://stackoverflow.com/questions/25519037/is-data-driven-testing-possible-with-protractor" target="_blank">this StackOverflow post</a>. The comment submitted by peterhendrick suggests the use of a JSON file containing the test data. I slightly simplified this to use a simple array of key-value pairs and have the Jasmine spec loop through the array. You can simply populate this array any way you like, for example from an external .json file as suggested, but in any other way as well. In this example, I hard-coded it in the spec itself:

<pre class="brush: javascript; gutter: false">describe("Calculator (data driven tests with test data specified in array)", function() {
  var calc;
  var testdata = [{"x":1,"y":1,"sum":2},{"x":2,"y":3,"sum":5},{"x":4,"y":2,"sum":6}];
  
  beforeEach(function() {
    calc = new Calculator();
  });

	for(var i in testdata) {
		it("should be able to add two numbers, using (" + testdata[i].x + "," + testdata[i].y + "," + testdata[i].sum + ")", function() {
			
			expect(calc.add(testdata[i].x, testdata[i].y)).toEqual(testdata[i].sum);
		});	
	};
});</pre>

I particularly like this example because it enables you to add the actual values used to the test output. You can see this when you run your spec:

<a href="http://www.ontestautomation.com/?attachment_id=1405" rel="attachment wp-att-1405"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_array.png" alt="Test results from driving test data through an array" width="729" height="175" class="aligncenter size-full wp-image-1405" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_array.png 729w, https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_array-300x72.png 300w" sizes="(max-width: 729px) 100vw, 729px" /></a>

**Data driven testing with a custom using function**  
<a href="http://blog.jphpsf.com/2012/08/30/drying-up-your-javascript-jasmine-tests" target="_blank">This blog post</a> from JP Castro presents an alternative way to do data driven testing in Jasmine. His solution seemed easy enough for me to apply directly to my own specs. Let&#8217;s see what it looks like.

The basic idea behind his solution is to wrap each it block in a spec with a using block that passes the parameter values to the it block:

<pre class="brush: javascript; gutter: false">describe("Calculator (data driven tests with using() function)", function() {
  var calc;
  
  beforeEach(function() {
    calc = new Calculator();
  });

  using(&#039;parameters&#039;,[[1,1,2],[2,3,5],[4,2,6]], function(parameters){
	it("should be able to add two numbers, using (" + parameters[0] + "," + parameters[1] + "," + parameters[2] + ")", function() {
		expect(calc.add(parameters[0],parameters[1])).toEqual(parameters[2]);
	});
  });
});</pre>

The using function he presents is implemented as follows:

<pre class="brush: javascript; gutter: false">function using(name, values, func) {
	for(var i = 0, count = values.length; i &lt; count; i++) {
		if(Object.prototype.toString.call(values[i]) !== &#039;[Object Array]&#039;) {
			values[i] = [values[i]];
		}
		func.apply(this,values[i]);
	}
}</pre>

When we run the spec above, the output looks like this:

<a href="http://www.ontestautomation.com/?attachment_id=1406" rel="attachment wp-att-1406"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_using.png" alt="Test results from driving test data through a using function" width="730" height="175" class="aligncenter size-full wp-image-1406" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_using.png 730w, https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_data_driven_test_results_using-300x72.png 300w" sizes="(max-width: 730px) 100vw, 730px" /></a>

So, now we have two simple (and admittedly similar) workarounds for what I think is still a fundamental shortcoming in Jasmine. The code demonstrated in this blog post can be downloaded <a href="http://www.ontestautomation.com/files/JasmineDataDriven.zip" target="_blank">here</a>.