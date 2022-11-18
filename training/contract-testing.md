---
title: Contract testing
layout: page
permalink: /training/contract-testing/
---
In a world where new software applications are increasingly often built upon a microservices-based architecture, where multiple teams are building, deploying and running services and components independently, it can be hard to verify whether all these individual services can still communicate with one another over time.

Contract testing is a technique, supported by tools, that can help you deploy your services with confidence in a highly distributed software system.

In this training course, you'll learn what contract testing is, where it fits into your overall testing strategy and how the Pact framework can help you set up and run contract tests.

**Course contents**  
This workshop covers, among other things:

_Context and background of contract testing_
* The challenges of integration testing in distributed environments
* What problem does contract testing actually solve?
* What's the place of contract testing in an overall testing and automation strategy?
* The three approaches to contract testing

_Consumer-driven contract testing (CDCT)_
* The CDCT flow
* About defining consumer expectations
* Creating contracts from consumer expectations in Pact
* Verifying consumer expectations on the provider side

_The Pact Broker_
* Different types of brokers
* Publishing consumer contracts to the broker
* Pulling contracts from the broker as a consumer and publishing verification results

_Integrating contract testing in build pipelines_
* Contract generation
* Contract verification
* Checking if it's safe to go to production with can-i-deploy

_Challenges in implementing consumer-driven contract testing_

_Bidirectional contract testing (BDCT)_
* The BDCT flow
* Generating and publishing a consumer contract
* Generating and publishing a provider contract
* Contract comparison 

There's no better way to learn than by doing, so you'll be presented with a variety of hands-on (programming) exercises throughout this course.

This course can be delivered using the Pact bindings in Java, C#, Python and JavaScript.

**Intended audience and prerequisite knowledge**  
This course is aimed at software development and testing practitioners that want to learn more about contract testing and how to create and execute contract tests with Pact. Some previous exposure to object-oriented software development and (API-level) test automation will make it easier for you to follow along.

**After this course**
After completing this contract testing course, participants will have a working knowledge of:

* The different types of contract testing and which flavour works where
* The position of contract testing in an overall software testing and automation strategy
* How to build consumer-driven contract tests with Pact
* How to use the Pact Broker to manage contracts and verification results
* The challenges of implementing consumer-driven contract testing
* How bidirectional contract testing works and how it addresses the challenges of CDCT
* How to make contract testing part of a CI/CD pipeline

**Delivery and group size**  
This is a live training course, available both on site and online.

If you would like to book me to teach this course in your organization, or if you have any additional questions, please send an email to bas@ontestautomation.com or use the contact form on [this page](/contact/).

For an overview of all training courses I have on offer, please visit the main [training page](/training/).