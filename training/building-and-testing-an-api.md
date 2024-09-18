---
title: Building and testing an API
layout: page
permalink: /training/building-and-testing-an-api/
---
APIs are the glue of the Internet these days. In 2022, API calls were responsible for 83% of all Internet traffic, and that number is not likely to decrease any time soon. Knowing how to build and test APIs is therefore very important (I'd even say essential) knowledge for every development team.

Unfortunately, despite the importance of APIs, a lot of API testing is still conducted in a very shallow way, and typically also quite late in the development process. After an API endpoint has been built and deployed into a test environment, we invoke some endpoints, check response HTTP status codes and some of the data, and then we often call it a day.

This course is meant to bring some change to that situation. In an iterative and highly interactive way, we are going to design, develop and test an API with several endpoints. Every iteration brings a new challenge, new tasks to complete and new tests and test types to consider and execute. In this way, we'll incrementally build a comprehensive API testing strategy, and learn more about good practices for API design and development in the process.

#### Course contents

During this course, we'll cover the following topics, among other things (but not necessarily in the listed order):

**API design**
* Contract-first or code-first API design and development?
* Good API design practices
* Making your API easy to understand, consume and extend

**API development**
* Setting up a new API project
* Implementing different routes for HTTP GET, POST, PUT / PATCH and DELETE
* Adding a database to your API

**API testing**
* Inspecting API specification documents
* Writing unit tests for logic in API controllers
* Writing tests for individual API endpoints and API scenarios
* Mocking databases and downstream services for testing purposes
* Building a build-test-deploy pipeline for our API
* An introduction to contract testing

#### Intended audience and prerequisite knowledge
This course is aimed at software testers and software developers who want to learn more about developing and testing APIs, and what it takes to do it well.

Some prior exposure to (object-oriented) software development and test automation tools and concepts will be helpful. I am confident, however, that even without this you will find this masterclass to be very useful.

#### After this course
After completing this course, you have a better understanding of what it takes to design, build and test an API. You'll have learned how to perform better API testing earlier in the development process, and you will have learned more about the different testing techniques that make up a solid API testing strategy. You’ll leave this course with a collection of tools, examples and resources for further studying that will help you in your day to day work.

#### Delivery and group size
This is a live training course, available both on site and online. I recommend a group of between 5 and 12 participants to optimize for collaboration opportunities and pace.

I can deliver this course using a number of technologies:

* C# .NET with ASP.NET Core and [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} or [RestSharp](https://restsharp.dev/){:target="_blank"} for acceptance tests and [NUnit](https://nunit.org/){:target="_blank"}, [xUnit](https://xunit.net/){:target="_blank"} or [MSTest](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-mstest){:target="_blank"} for unit tests and as a test runner
* Java with Spring and [REST Assured](https://rest-assured.io/){:target="_blank"} for acceptance tests and [JUnit](https://junit.org/junit5/){:target="_blank"} or [TestNG](https://testng.org/){:target="_blank"} for unit tests and as a test runner
* Python with FastAPI or Flask and [requests](https://requests.readthedocs.io/en/latest/){:target="_blank"} for acceptance tests and [unittest](https://docs.python.org/3/library/unittest.html){:target="_blank"} or [pytest](https://docs.pytest.org/en/stable/){:target="_blank"} for unit tests and as a test runner
* JavaScript / TypeScript with Express and [Axios](https://axios-http.com/docs/intro){:target="_blank"} or [Pactum](https://pactumjs.github.io/){:target="_blank"} for acceptance tests and [Jest](https://jestjs.io/){:target="_blank"} or [Mocha](https://mochajs.org/){:target="_blank"} for unit tests and as a test runner

Can't see your preferred technology stack in the list? Other technologies and libraries are available on demand.

If you would like to book me to teach this course in your organization, or if you have any additional questions, please send an email to bas@ontestautomation.com or use the contact form on [this page](/contact/).