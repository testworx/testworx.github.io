---
layout:     post
title:      "Auto Generating JMeter Test Plans Pt. 1"
subtitle:   "Recording & Correlation"
date:       2016-06-01 12:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<p>Recently my team and I faced an issue where we were going to have do frequent early performance testing as part of a large upgrade project.  Whilst it was great to be given the opportunity to do this early performance testing, we didn't want to get into the mess of trying to maintain performance tests for an application that was still being developed, in addition to maintaining our functional automated test pack.</p>

<p>To get around this we decided to use our functional automated tests to drive the application and use JMeter to record the traffic which would form the basis of our performance test scripts.  The reasoning was that we could then focus on our automated test maintenance as normal, and carry out performance testing as required, but treat our performance test scripts as disposable assets that we could regenerate quickly as required.</p>

<p>There are 4 main parts to this process:
<ol>
<li>Recording the Test Plan</li>
<li>Correlation</li>
<li>Post Processing Using Groovy</li>
<li>Replay & Debugging</li>
</ol></p>

<h2>Git Repository</h2>
<p>A GitHub repository is available containing all the files used in this blog post:  <a href="https://github.com/testworx/jmeter-test-plan-generator">here</a></p>

<h2>Recording</h2>
<p>Recording browser traffic with JMeter using a <a href="http://jmeter.apache.org/usermanual/component_reference.html#HTTP(S)_Test_Script_Recorder">Proxy Recorder</a> is well documented on the <a href="http://jmeter.apache.org/usermanual/jmeter_proxy_step_by_step.pdf">JMeter website</a> and a sample test plan is available in the the Github repo above in <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/Recording.Template.jmx"><i>src/main/resources/Recording.Template.jmx</i></a> to make the job easier.</p>
<p>it might not be obvious how to route your automated tests through JMeter so that traffic can be recorded.  To do this we will specify a proxy when creating the browser using Geb.  Note that the same method is used with Selenium.  An example GebConfig file can be found in <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/test/resources/GebConfig.groovy"><i>src/test/resources</i></a> that will send browser traffic through JMeter.</p>

<h2>Correlation</h2>
<p>Once the script has been recorded, you need to go through and identify any session specific values for correlation.  <a href="http://jmeter.apache.org/usermanual/component_reference.html#Regular_Expression_Extractor">JMeter Regular Expression Extractors</a> are ideal for pulling this data from a response and making it available for future requests.</p>
<p>I tend to have two JMeter session open at this point, one with the recorded script in <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/Recording.Template.jmx"><i>src/main/resources/Recording.Template.jmx</i></a>, and one with <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/TestPlan.Template.jmx"><i>src/main/resources/TestPlan.Template.jmx</i></a> open.  As items for correlation are identified (values that look like random sequences of letters and numbers are a good bet!), find the first response that returns that value in the View Results Tree Listener under the Workbench (in the Recording Test Plan).  Construct a regular expression that uniquely identifies that value in the response and put it into a new Regular Expression Extractor in the root of the test plan within TestPlan.Template.jmx.  Putting it in the root of the test plan means that it will apply to all responses.</p>

<p>Tune in for Part 2 where we discuss the structure of the XML underlying the JMeter test plan that has been recorded as well as programmatically parsing it into a new test plan that uses the values we correlated earlier.</P>
