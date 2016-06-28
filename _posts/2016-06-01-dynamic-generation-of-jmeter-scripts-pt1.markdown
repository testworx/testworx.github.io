---
layout:     post
title:      "Auto Generating JMeter Test Plans Pt. 1"
subtitle:   "Recording a test plan using Geb"
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

<p>
  <ol>
    <li>clone the <a href="https://github.com/testworx/jmeter-test-plan-generator">project</a> and navigate to the root of the project on the command line.</li>
    <li>Run the following command:  <i>gradlew jmGui -Precord</i> - jmeter should now be open.</li>
    <li>Navigate to the Workbench.</li>
    <li>Select HTTP(S) Test Script Recorder and click Start.
    <img src="/assets/img/jmeter_test_plans/proxy_recorder.png" style="width:650px" /></li>
    <li>Click Ok on the Root CA Certificate popup<br>
    <img src="/assets/img/jmeter_test_plans/ca_cert_popup.png" style="width:580px" /></li>
    <li>You are now ready to start recording traffic from an automated test.</li>
    <li>Open another command prompt and navigate to the root of the project folder again.</li>
    <li>Run <i>gradlew firefoxJmeterTest</i> - Firefox should open and run the demo test against Google.</li>
    <li>Once the test has finished, switch to JMeter and view the transactions that have been recorded under the Recording Controller and click Save.
    <img src="/assets/img/jmeter_test_plans/recorded_transactions.png" style="width:650px" /></li>
    <li>We have now recorded our initial test plan ready for correlation and post processing</li>
  </ol>
</p>

<p>Tune in for Part 2 where we discuss correlation and post processing.</P>
