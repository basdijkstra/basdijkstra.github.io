---
id: 943
title: 'On designing a self-diagnosing and self-healing automated test framework &#8211; part 2'
date: 2015-08-15T12:55:08+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=943
permalink: /?p=943
categories:
  - General test automation
tags:
  - java
  - robustness
  - self-diagnosis
  - self-healing
  - test automation
---
_This is the second post in a short series on designing robust, self-diagnosing and, if possible, even self-healing tests. You can read all articles in this series by clicking <a href="http://www.ontestautomation.com/tag/self-diagnosis/" target="_blank">here</a>._

In the previous post, I introduced the concept of state with regards to a test automation framework. Having state should make it easier for automated tests to:

  * Know what state they are in (hello there Captain Obvious)
  * Transition from a running to an error state whenever an error occurs
  * Leave it to the test automation designer to decide what constitutes an error state
  * Differentiate between failed checks and runtime errors, such as ElementNotFound or synchronization issues
  * <a href="https://testautomationpatterns.wikispaces.com/FAIL+GRACEFULLY" target="_blank">Fail gracefully</a>, provide feedback about why a test run failed and perform necessary teardown actions 
      * And, if possible, recover from the error and continue test execution without human intervention (self-healing)</ul> 
    In this post I want to address the first two bullet points from the list above. I will do this using a simple test on the Parabank demo application that performs the following steps:
    
      1. Login to the application
      2. Go to the &#8216;Find Transactions&#8217; screen
      3. Search for the transaction with ID 1 for account 12345
      4. Check the message stating this transaction cannot be found
      5. Log out and close the browser
    
    &#8212; insert code &#8212;
    
    **Have your tests know what state they are in**  
    To have our test &#8216;know&#8217; what state they are in, we can simply use the state model as it has been introduced in the <a href="http://www.ontestautomation.com/on-designing-a-self-diagnosing-and-self-healing-automated-test-framework-part-1/" target="_blank">previous post</a> in this series and enrich our test with the correct states and state transitions. Note that consistency is the most important part in deciding when a test should enter or leave a certain state, i.e., when you have a series of tests that all include the same initialization procedure, you should change the state to &#8216;initialization&#8217; at the same moment for all tests. Of course, it&#8217;s even better to only describe that initialization procedure just once and use it in all of your tests.
    
    As an example, here&#8217;s the first part of our test again, but now with the initialization and use of our state object. Note the state transition to &#8216;INITIALIZATION&#8217; when the browser object is created and configured and then the transition to &#8216;RUNNING&#8217; when the actual test execution starts.
    
    &#8212; insert code &#8212;
    
    Again, the states and state transitions I have chosen for this examples are just that: examples. You&#8217;re free to define and choose your own. I think it&#8217;s a good idea to have a separate initialization state, especially when your test requires more intricate setup steps such as dynamic test data selection or creation.
    
    When we run this test and look at the console output, we clearly see the state transitions occurring:
    
    &#8212; insert screenshot &#8212;
    
    **Transition into an error state whenever an error occurs**  
    The state model becomes especially useful when we try to handle errors that occur during test execution. As an example I&#8217;ll consider the situation where an element that is used in our test cannot be found on the page. To prevent code duplication, let&#8217;s create a simple helper method that tries to find a specified element and has the test transition to an error state whenever that element cannot be found:
    
    &#8212; insert code &#8212;
    
    We can use this helper method whenever we try to handle a web page element in our tests:
    
    &#8212; insert code &#8212;
    
    The second time the helper method is called, the specified object cannot be found and the test transitions to the ERROR state, as can be seen in the console output:
    
    &#8212; insert screenshot &#8212;
    
    CONTINUE HERE
    
    **Difference in behaviour between stateful and stateless tests**