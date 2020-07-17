---
id: 1929
title: Creating executable specifications with Spectrum
date: 2017-07-18T09:59:53+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1926-revision-v1/
permalink: /1926-revision-v1/
---
One of the most important features of a good set of automated tests is that they require a minimal amount of explanation and/or documentation. When I see someone else&#8217;s test, I&#8217;d like to be able to instantly see what its purpose is and how it&#8217;s constructed in terms of setting up > execution > verification (for example using given/when/then or arrange/act/assert). That&#8217;s one of the reasons that I&#8217;ve been using Cucumber (or SpecFlow, depending on the client) in several of my automated test solutions, even though the team wasn&#8217;t doing Behaviour Driven Development.

It&#8217;s always a good idea to look for alternatives, though. Last week I was made aware of Spectrum, which is, as creator <a href="https://github.com/greghaskins" target="_blank">Greg Haskins</a> calls it _&#8220;A BDD-style test runner for Java 8. Inspired by Jasmine, RSpec, and Cucumber.&#8221;_. I&#8217;ve been working with Jasmine a little before, and even though it took me a while to get used to the syntax and lambda notation style, I liked the way it provided a way to document your tests directly in the code and produce human readable results. Since Spectrum is created as a Jasmine-like test runner for Java, plus it provides support for the (at least in the Java world) much more common Gherkin given/when/then syntax, I thought it&#8217;d be a good idea to check it out.

Spectrum is &#8216;just&#8217; yet another Java library, so adding it to your project is a breeze when using Maven or Gradle. Note that since Spectrum uses lambda functions, it won&#8217;t work unless you&#8217;re using Java 8. Spectrum runs on top of JUnit, so you&#8217;ll need that too. It works with all kinds of assertion libraries, so if you&#8217;re partial to Hamcrest, for example, that&#8217;s no problem at all.

As I said, Spectrum basically supports two types of specs: the Jasmine-style describe/it specification and the Gherkin-style given/when/then specification using features and scenarios. Let&#8217;s take a quick look at the Jasmine-style specifications first. For this example, I&#8217;m resorting once again to REST Assured tests. I&#8217;d like to verify whether Max Verstappen is in the list of 2017 drivers in one test, and whether both Fernando Alonso and Lewis Hamilton are in that list too in another test. This is how that looks like with Spectrum:

<pre class="brush: java; gutter: false">@RunWith(Spectrum.class)
public class SpectrumTests {{

	describe("The list of drivers for the 2017 season", () -&gt; {

		ValidatableResponse response = get("http://ergast.com/api/f1/2017/drivers.json").then();

		String listOfDriverIds = "MRData.DriverTable.Drivers.driverId";

		it("includes Max Verstappen", () -&gt; {

			response.assertThat().body(listOfDriverIds, hasItem("max_verstappen"));
		});

		it("also includes Fernando Alonso and Lewis Hamilton", () -&gt; {

			response.assertThat().body(listOfDriverIds, hasItems("alonso","hamilton"));
		});
	});
}}</pre>

Since Spectrum runs &#8216;on top of&#8217; JUnit, executing this specification is a matter of running it as a JUnit test. This results in the following output:

<a href="http://www.ontestautomation.com/?attachment_id=1927" rel="attachment wp-att-1927"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_jasmine_output.png" alt="Spectrum output for Jasmine-style specs" width="576" height="215" class="aligncenter size-full wp-image-1927" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_jasmine_output.png 576w, https://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_jasmine_output-300x112.png 300w" sizes="(max-width: 576px) 100vw, 576px" /></a>

Besides this (admittedly quite straightforward) example, Spectrum also comes with support for setup (using _beforeEach_ and _beforeAll_) and teardown (using _afterEach_ and _afterAll_), focusing on or ignoring specific specs, tagging specs, and more. You can find the documentation <a href="https://github.com/greghaskins/spectrum/blob/1.1.1/docs/SpecificationDSL.md" target="_blank">here</a>.

The other type of specification supported by Spectrum is the Gherkin syntax. Let&#8217;s say I want to recreate the same specifications as above in the given/when/then format. With Spectrum, that looks like this:

<pre class="brush: java; gutter: false">@RunWith(Spectrum.class)
public class SpectrumTestsGherkin {{

	feature("2017 driver list verification", () -&gt; {

		scenario("Verify that max_verstappen is in the list of 2017 drivers", () -&gt; {

			final Variable&lt;String&gt; endpoint = new Variable&lt;&gt;();
			final Variable&lt;Response&gt; response = new Variable&lt;&gt;();

			given("We have an endpoint that gives us the list of 2017 drivers", () -&gt; {
				
				endpoint.set("http://ergast.com/api/f1/2017/drivers.json");
			});

			when("we retrieve the list from that endpoint", () -&gt; {
				
				response.set(get(endpoint.get()));
			});
			then("max_verstappen is in the driver list", () -&gt; {
				
				response.get().then().assertThat().body("MRData.DriverTable.Drivers.driverId", hasItem("max_verstappen"));
			});
		});

		scenarioOutline("Verify that there are also some other people in the list of 2017 drivers", (driver) -&gt; {

			final Variable&lt;String&gt; endpoint = new Variable&lt;&gt;();
			final Variable&lt;Response&gt; response = new Variable&lt;&gt;();

			given("We have an endpoint that gives us the list of 2017 drivers", () -&gt; {
				
				endpoint.set("http://ergast.com/api/f1/2017/drivers.json");
			});

			when("we retrieve the list from that endpoint", () -&gt; {
				
				response.set(get(endpoint.get()));
			});
			then(driver + " is in the driver list", () -&gt; {
				
				response.get().then().assertThat().body("MRData.DriverTable.Drivers.driverId", hasItem(driver));
			});
		},

		withExamples(
				example("hamilton"),
				example("alonso"),
				example("vettel")
			)
		);
	});
}}</pre>

Running this spec shows that it does work indeed:

<a href="http://www.ontestautomation.com/?attachment_id=1928" rel="attachment wp-att-1928"><img src="http://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_gherkin_output.png" alt="Spectrum output for Gherkin-style specs" width="719" height="580" class="aligncenter size-full wp-image-1928" srcset="https://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_gherkin_output.png 719w, https://www.ontestautomation.com/wp-content/uploads/2017/07/spectrum_gherkin_output-300x242.png 300w" sizes="(max-width: 719px) 100vw, 719px" /></a>

There are two things that are fundamentally different from using the Jasmine-style syntax (the rest is &#8216;just&#8217; syntactical):

  * Support for scenario outlines enables you to create data driven tests easily. Maybe this can be done too using the Jasmine syntax, but I haven&#8217;t figured it out so far.
  * If you want to pass variables between the given, the when and the then steps you&#8217;ll need to do so by using the _Variable_ construct. This works with the Jasmine-style syntax too, but you&#8217;ll likely need to use it more in the Gherkin case (since &#8216;given/when/then&#8217; are three steps, where &#8216;it&#8217; is just one). When your tests get larger and more complex, having to use _get()_ and _set()_ every time you want to access or assign a variable might get cumbersome.

Having said that, I think Spectrum is a good addition to the test runner / BDD-supporting tool set available for Java, and something that you might want to consider using for your current (or next) test automation project. After all, any library or tool that makes your tests and/or test results more readable is worth taking note of. Right?

You can find a small Maven project containing the examples featured in this blog post <a href="http://www.ontestautomation.com/files/SpectrumDemo.zip" target="_blank">here</a>.