---
id: 279
title: Generating automated tests from specifications
date: 2014-01-31T13:03:57+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=279
permalink: /generating-automated-tests-from-specifications/
categories:
  - General test automation
tags:
  - test automation
  - test case generation
---
In most cases, the implementation of automated testing focuses on the automated execution of previously defined test cases. These test cases come in the form of test scripts or use cases that are prepared by test analists or test engineers. Often, before the introduction of automated testing in a project and / or team, these test cases were executed by hand in the past.

However, what if we could also automate the creation of test cases? When applied correctly, this would yield an even more efficient testing process. Furthermore, the number of mistakes in the derivation of test cases would be reduced to 0 (given that the test case generator is functioning correctly). These two factors indicate that an interesting case might be made, given the right circumstances.

Of course, this does not apply everywhere. To be able to automatically generate test cases, the following points should be observed:

_Are my specifications recorded in a format that is suitable for automated test case generation?_  
Of course, the specifications should be recorded in such a way that they can be interpreted by your test case generator. It is highly unlikely that specifications written in natural language in a Word document can be read and understood by a piece of code to the extent that correct test case specifications can be derived from it (however, if you have ever achieved this, please let me know :). On the other hand, when (part of) the specifications for your SUT are captured in a formal model, certain types of test cases might be derived from this by an intelligent piece of software. This is sometimes achieved in [Model Based Testing](https://en.wikipedia.org/wiki/Model_based_testing) approaches.

Also, in order to validate whether business rules are implemented correctly in your SUT, it might be feasible to automatically derive test cases from these rules, given that they are specified in some sort of (semi-)formal language.

I&#8217;m sure there are more examples where generating test cases with a single keystroke or mouse click is feasible.

_Is there a profit to be gained from the ability to generate test cases automatically?_  
Of course, the fact that something can be automated doesn&#8217;t mean it necessarily should be automated. However, when there are a lot of test cases to be generated, or when specs change frequently throughout the software development process, which occurs more and more often with the rise in popularity of Agile and Scrum methodologies, developing and testing a test case generator might be beneficial. As with the previous factor, I&#8217;m sure there are more possibilities for profit here, but these two stand out for me at the moment.

**A case study**  
In a recent project I have been able to successfully implement not only the automated execution of test cases, but also the generation of these test cases directly from the specifications as provided by our client.

The project concerned the testing of a web service that validates fixed-length messages for syntactical correctness, semantical integrity and adherence to a number of business rules. There were around 20 types of messages, each consisting of a couple of hundreds of fields. Each field and each business rule has a corresponding error code that is returned by the server in case either the field specifications or the business rules are violated. Needless to say, that&#8217;s a lot of test cases.

Specifying these test cases by hand would take a very long time, would be tiresome as the test cases are highly similar and would therefore be pretty error-prone as well. Even testers get bored every now and then.. However, the similarity and the number of the set of test cases also makes a pretty good case for automating test case generation.

First, we need to determine what types of test cases we can identify and which of these can be generated automatically. In this article, I will focus on the negative test cases, i.e., the test cases for which the web service should return one or more error codes. For this project, I identified the following types of negative test cases:

  * Leave mandatory fields empty
  * Assign alphanumeric values to numeric fields
  * For fields that may only contain values from a predefined enumeration: enter values that are not in the enumeration
  * Simple business rules such as &#8216;IF field A contains value X THEN field B should contain value Y&#8217;
  * More complex business rules such as &#8216;The message may contain up to three address records whenever the client is of type Z&#8217;

I chose to skip the test case where the mandatory field was omitted altogether. In fixed length messages, this will cause all subsequent fields to be out of place, effectively rendering the message useless.

Next, I wrote some lines of code (in Jython, in my case) to read the message specifications (these were provided in Excel format, not ideal but manageable using the Apache POI library) and derive test cases from them. For example, a field with the following characteristics:

  * Mandatory
  * Length 1
  * Numerical
  * Values allowed: 1,2,9

yields the following negative test cases:

  * Leave the field empty
  * Enter value &#8216;A&#8217; in the field
  * Enter value 3 in the field

Writing the code to generate these test cases took me a day, far shorter than it would have taken to generate all test cases by hand. Let alone what would happen when a new version of the message specs would have arrived somewhere halfway through the testing process..

Next, I had to generate the test messages for each and every test case. I did this by using a test message template, which was effectively a valid message (i.e., it does not generate error messages upon validation), containing all possible fields. Then, for every negative test case, I replaced the correct value for the field under test in that test case with an incorrect value depending on the type of test case. For example, when the test message template looks like this:

<pre>10002398TEST141401</pre>

and the field under test is the last position in the test message, the generated test messages for the test cases mentioned above look like this:

<pre>10002398TEST14140
10002398TEST14140A
10002398TEST141403</pre>

Then, I&#8217;d send the test message to the validation web service and checked whether the response from the web service contained a message stating that the field under test had an error in it, and whether the correct error code was returned by the service.

As a result, I was able to quickly generate literally thousands of test cases, many of which had never been executed before (as there was no time and no-one had though of generating test cases instead of manually specifying them). This resulted in very high test coverage and quick turnaround time, and a very happy customer overall.

Hopefully the case study above shows an example of the power of generating test cases. Again, it might not be achievable or economical everywhere, but when it does, the results may be very beneficial. Whenever system specifications are available in a format that can be processed automatically (such as Excel, text or XML), there might just be an opportunity to save lots of time using test case generation.