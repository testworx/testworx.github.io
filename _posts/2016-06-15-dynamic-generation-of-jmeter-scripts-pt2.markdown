---
layout:     post
title:      "Auto Generating JMeter Test Plans Pt. 2"
subtitle:   "Post Processing Using Groovy"
date:       2016-06-15 12:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<p>This is a continuation of <a href="{% post_url 2016-06-01-dynamic-generation-of-jmeter-scripts-pt1 %}">part 1</a> of this article where we recorded a test plan using Geb or Selenium to drive the application under test.  After performing some initial correlation we are now ready to do some post processing on the recorded test plan file using Groovy.</p>

<p>To get around this we decided to use our functional automated tests to drive the application and use JMeter to record the traffic which would form the basis of our performance test scripts.  The reasoning was that we could then focus on our automated test maintenance as normal, and carry out performance testing as required, but treat our performance test scripts as disposable assets that we could regenerate quickly as required.</p>

<h2>Git Repository</h2>
<p>A git repository is available containing all the files used in this blog post:  <a href="https://github.com/testworx/jmeter-test-plan-generator">here</a></p>

<h2>Correlation</h2>
<p>Once the script has been recorded, you need to go through and identify any session specific values for correlation.  <a href="http://jmeter.apache.org/usermanual/component_reference.html#Regular_Expression_Extractor">JMeter Regular Expression Extractors</a> are ideal for pulling this data from a response and making it available for future requests.</p>
<p>I tend to have two JMeter session open at this point, one with the recorded script in <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/Recording.Template.jmx"><i>src/main/resources/Recording.Template.jmx</i></a>, and one with <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/TestPlan.Template.jmx"><i>src/main/resources/TestPlan.Template.jmx</i></a> open.  As items for correlation are identified (values that look like random sequences of letters and numbers are a good bet!), find the first response that returns that value in the View Results Tree Listener under the Workbench (in the Recording Test Plan).  Construct a regular expression that uniquely identifies that value in the response and put it into a new Regular Expression Extractor in the root of the test plan within TestPlan.Template.jmx.  Putting it in the root of the test plan means that it will apply to all responses.</p>

<h2>Post Processing</h2>
<p>Once the script has been recorded, and after we have done some initial correlation, it is time to programmatically create a new JMeter test plan based on our test plan template (TestPlan.Template.jmx) which has had Regular Expression Extractors added for all parameters we think are dynamic, and the recorded transactions from our original recording test plan (Recording.Template.jmx)</P>
<p>To do this we will create a Groovy script (although any scripting language would do) to carry out the following:
  <ol>
  <li>Read in Recording.Template.jmx</li>
  <li>Read in TestPlan.Template.jmx</li>
  <li>Iterate through the recorded transactions and replace the correlated values with the name of the regular expression parameter in the format '${REGEX_PARAMETER_NAME}'</li>
  <li>Write the contents of TestPlan.Template.jmx into a new test plan.</li>
  <li>Write the recorded transactions into this new test plan.</li>
  <li>Open the test plan in JMeter and check that everything appears to be correlated correctly.</li>
  </ol>
</p>

<h2>Replay and Debugging</h2>
<p>Play back the final script that has been parameterised and verify that the data is appearing correctly within the environment.</p>
