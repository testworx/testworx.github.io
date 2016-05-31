---
layout:     post
title:      "Auto Generating JMeter Test Plans Pt. 2"
subtitle:   "Post Processing Using Groovy"
date:       2016-06-15 12:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<p>This is a continuation of <a href="../../../2016/06/01/dynamic-generation-of-jmeter-scripts-pt1//">part 1</a> of this article where we recorded a test plan using Geb or Selenium to drive the application under test.  After performing some initial correlation we are now ready to do some post processing on the recorded test plan file using Groovy.</p>

<p>To get around this we decided to use our functional automated tests to drive the application and use JMeter to record the traffic which would form the basis of our performance test scripts.  The reasoning was that we could then focus on our automated test maintenance as normal, and carry out performance testing as required, but treat our performance test scripts as disposable assets that we could regenerate quickly as required.</p>

<h2>Git Repository</h2>
<p>A git repository is available containing all the files used in this blog post:  <a href="https://github.com/testworx/jmeter-test-plan-generator">here</a></p>

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
