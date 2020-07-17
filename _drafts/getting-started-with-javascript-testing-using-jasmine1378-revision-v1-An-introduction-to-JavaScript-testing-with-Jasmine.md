---
id: 1394
title: An introduction to JavaScript testing with Jasmine
date: 2016-04-13T06:37:33+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1378-revision-v1/
permalink: /1378-revision-v1/
---
New project, new environment, new tools to explore! I just started working as a freelance test engineer on a new project involving some of Holland&#8217;s most visited job sites. An environment where a lot of PHP and JavaScript is used, as opposed to the familiar Java and C#. This also means I get to learn a lot of new stuff (I&#8217;ve not worked much in web development projects before) and a lot of new and interesting test automation tools and concepts to explore and apply. Which I can then use to bore you.

The first tool I get to explore is <a href="http://jasmine.github.io/" target="_blank">Jasmine</a>, a BDD-style tool for testing JavaScript code. As with most other BDD tools, it&#8217;s only truly a BDD tool if you use it as such and I&#8217;m not yet convinced that we&#8217;re doing that right in this project. However, Jasmine offers enough interesting features for a blog post, so here we go..

**What is Jasmine and where can I get it?**  
Simply put, Jasmine is a stand-alone JavaScript testing tool that supports writing your tests in a BDD-like manner. It does not require a DOM and therefore a browser to run, which makes tests run incredibly fast. Its syntax is also pretty clear and simple, as we will see in this post.

You can download the latest Jasmine release from <a href="https://github.com/jasmine/jasmine/releases" target="_blank">here</a>, after which you can unzip it. The package contains the Jasmine source (also written in JavaScript) and some sample code and tests.

**The code to be tested**  
My example code that will be tested is a simple _Car_ object that supports a two methods, one to add a driver to a car and one to start driving the car:

<pre class="brush: javascript; gutter: false">function Car() {
}

Car.prototype.addDriver = function() {
  if (this.hasDriver) {
	  throw new Error("Someone&#039;s already driving this car");
  }
  if (this.isDriving) {
	  throw new Error("Can&#039;t add a driver when the car is driving");
  }
  this.hasDriver = true;
};

Car.prototype.startDriving = function() {
	this.isDriving = true;
}</pre>

**A first Jasmine test**  
Let&#8217;s say we want to write a simple test to see whether we can actually add a driver to a car. In Jasmine, this is done as follows:

<pre class="brush: javascript; gutter: false">describe("A Car", function() {

  var car;
  var person;

  beforeEach(function() {
    car = new Car();
    person = new Person();
  });

  it("should be able to be assigned a driver", function() {
	car.addDriver(person);
	expect(car.hasDriver).toBeTruthy();
  });
});</pre>

The _describe_ keyword starts a new test suite and takes two parameters: the first is a string providing a description for the test suite, the second is a method that implements the test suite. A test suite consists of a number of tests (or specs in Jasmine terminology), each defined using the _it_ keyword. Just as with Java testing frameworks such as JUnit and TestNG, you have the option to perform setup and teardown actions, in this case using _beforeEach_.

Assertions are performed using the _expect_ keyword and a matcher, in this case _toBeTruthy()_. This matcher checks whether the value of a variable evaluates to _true_ (note that values such as 1 and &#8216;test&#8217; also evaluate to true in JavaScript). Jasmine comes with a rich set of matchers, which are described <a href="http://jasmine.github.io/2.4/introduction.html" target="_blank">here</a>.

**Running your tests**  
Running Jasmine tests can be done by creating an HTML file that includes both the source code and the specs files in the 

<head>
  and opening it in a browser. The HTML runner file for my demo project looks like this:</p> 
  
  <pre class="brush: html; gutter: false">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
  &lt;meta charset="utf-8"&gt;
  &lt;title&gt;Jasmine Car Demo Spec Runner v2.4.1&lt;/title&gt;

  &lt;link rel="shortcut icon" type="image/png" href="lib/jasmine-2.4.1/jasmine_favicon.png"&gt;
  &lt;link rel="stylesheet" href="lib/jasmine-2.4.1/jasmine.css"&gt;

  &lt;!-- include Jasmine --&gt;
  &lt;script src="lib/jasmine-2.4.1/jasmine.js"&gt;&lt;/script&gt;
  &lt;script src="lib/jasmine-2.4.1/jasmine-html.js"&gt;&lt;/script&gt;
  &lt;script src="lib/jasmine-2.4.1/boot.js"&gt;&lt;/script&gt;

  &lt;!-- include source files --&gt;
  &lt;script src="src/Car.js"&gt;&lt;/script&gt;
  &lt;script src="src/Person.js"&gt;&lt;/script&gt;

  &lt;!-- include spec files --&gt;
  &lt;script src="spec/SpecHelper.js"&gt;&lt;/script&gt;
  &lt;script src="spec/CarSpec.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body/&gt;
&lt;/html&gt;</pre>
  
  <p>
    When we open this HTML file in our browser, the specs are run and we can see the following result:
  </p>
  
  <p>
    <a href="http://www.ontestautomation.com/?attachment_id=1387" rel="attachment wp-att-1387"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_1.png" alt="Results of first Jasmine test run" width="758" height="154" class="aligncenter size-full wp-image-1387" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_1.png 758w, https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_1-300x61.png 300w" sizes="(max-width: 758px) 100vw, 758px" /></a>
  </p>
  
  <p>
    Hooray!
  </p>
  
  <p>
    By the way, if the assertion would fail, the Jasmine output would look like this:
  </p>
  
  <p>
    <a href="http://www.ontestautomation.com/?attachment_id=1390" rel="attachment wp-att-1390"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_failure.png" alt="Example of a failing Jasmine check" width="754" height="388" class="aligncenter size-full wp-image-1390" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_failure.png 754w, https://www.ontestautomation.com/wp-content/uploads/2016/04/jasmine_test_results_failure-300x154.png 300w" sizes="(max-width: 754px) 100vw, 754px" /></a>
  </p>
  
  <p>
    <strong>Checking for expected errors</strong><br /> Returning to the code for the Car object, we can see that some methods throw errors. If you want to check that these errors are indeed thrown, you can do that in Jasmine like this:
  </p>
  
  <pre class="brush: javascript; gutter: false">it("should throw an error when assigned a second driver", function() {
  car.addDriver(person);
  
  expect(function() {
	  car.addDriver(person);
  }).toThrowError("Someone&#039;s already driving this car");
});</pre>
  
  <p>
    <strong>Adding and using custom matchers</strong><br /> Even though Jasmine includes a variety of matchers to perform all kinds of checks, sometimes you might have to perform very specific checks not covered by one of the standard matchers. In this case, it&#8217;s also possible to write a custom matcher (which is called <em>toBeDriving</em> in this case):
  </p>
  
  <pre class="brush: javascript; gutter: false">beforeEach(function () {
  jasmine.addMatchers({
    toBeDriving: function () {
      return {
        compare: function (actual, expected) {
          var car = actual;

          return {
            pass: car.isDriving
          };
        }
      };
    }
  });
});</pre>
  
  <p>
    This custom matcher can then be used in Jasmine specs just like any other matcher:
  </p>
  
  <pre class="brush: javascript; gutter: false">it("should be able to start driving", function() {
  car.startDriving();
  expect(car).toBeDriving();		
});</pre>
  
  <p>
    Jasmine offers lots more features, such as the ability to <a href="http://jasmine.github.io/2.4/custom_reporter.html" target="_blank">write custom reporters</a>. Check it out!
  </p>
  
  <p>
    The example project I have used in this blog post can be downloaded here. It includes Jasmine 2.4.1 and a working HTML runner, so you can start experimenting with Jasmine right away.
  </p>