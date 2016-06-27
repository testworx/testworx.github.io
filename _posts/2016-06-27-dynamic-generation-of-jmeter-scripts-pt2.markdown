---
layout:     post
title:      "Auto Generating JMeter Test Plans Pt. 2"
subtitle:   "Correlation"
date:       2016-06-27 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<p>This is a continuation of <a href="{% post_url 2016-06-01-dynamic-generation-of-jmeter-scripts-pt1 %}">part 1</a> where we recorded a JMeter test plan using Geb to drive the application under test.  We are now ready to do some initial correlation and post processing using Groovy.</p>

<h2>Git Repository</h2>
<p>A git repository is available containing all the files used in this blog post:  <a href="https://github.com/testworx/jmeter-test-plan-generator">here</a></p>

<h2>Correlation</h2>
<p>Once the script has been recorded, you need to go through and identify any session specific values for correlation.  <a href="http://jmeter.apache.org/usermanual/component_reference.html#Regular_Expression_Extractor">JMeter Regular Expression Extractors</a> are ideal for pulling this data from a response and making it available for future requests.</p>
<p>I tend to have two JMeter sessions open at this point, one with the recorded script in <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/Recording.Template.jmx"><i>src/main/resources/Recording.Template.jmx</i></a>, and one with <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/TestPlan.Template.jmx"><i>src/main/resources/TestPlan.Template.jmx</i></a> open.  As items for correlation are identified, find the first response that returns that value in the View Results Tree Listener under the Workbench (in the Recording Test Plan).  Construct a regular expression that uniquely identifies that value in the response and put it into a new Regular Expression Extractor in the root of the test plan within TestPlan.Template.jmx.  Putting it in the root of the test plan means that it will apply to all responses.</p>

<p>In the case of the test plan recorded earlier, we did a search for Wikipedia then clicked on the first result which took us to Wikipedia.org which has been hard coded in the recorded test plan.  For the purposes of this post, lets say we wanted to dynamically determine what URL the first result would take us to, and then browse to that URL.  We would need to do the following:

<ol>
  <li>Correlate the URL using a regular expression extractor</li>
  <li>Add this extractor to the <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/TestPlan.Template.jmx"><i>TestPlan.Template.jmx</i></a></li>
  <li>Write a groovy script that will perform correlation using this extractor automatically on test plans we record in the future.</li>
</ol>

<h2>Correlate the URL using a regular expression extractor.</h2>
<p>When we perform a Google search we click one of the result links and go to the relevant URL.  To simulate this using JMeter we need to identify the request that gives us this URL and correlate it so we can pull it out dynamically.</p>
<p>In the screenshot below we can see that the URL is given to us in the response to request 51.  Note that this request number is likely to change when you record the test yourself.<br>
<img src="/assets/img/jmeter_test_plans/correlation_1.png" style="width:580px" /></p>
<p>Once we know which response gives us our result URL we can construct a regular expression to pull out what we want.  In this instance, the following gives us what we need: '<i>x22([a-z]+):\\\/\\\/(.+?)\\\/\\\\x22</i>'.
I don't claim to be an expert at regexes so I am sure there is a more elegant verion but this works (at the time of writing).
<img src="/assets/img/jmeter_test_plans/correlation_2.png" style="width:580px" /></p>

<h2>Add a Regular Expression Extractor to the Test Plan Template</h2>
<p>Now we have identified the regular expression that will pull out the information we need, we need to add this to our <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/resources/TestPlan.Template.jmx"><i>test plan template</i></a>.</p>
<p>The following screenshot shows the configuration that should be used for the Regular Expression Extractor.<img src="/assets/img/jmeter_test_plans/regex_1.png" style="width:580px" /></p>
<p>It is worth briefly explaining the different parameters we have used in this post processor.
<ul>
  <li><b>Reference Name</b> - this is the name by which we will reference the extracted value.</li>
  <li><b>Regular Expression</b> - this is the expression used to extract the data we want to use.</li>
  <li><b>Temple</b> - In the expression we are using, there are actually two parts:  <b>([a-z]+)</b> & <b>(.+?)</b>.  The template allows us to define which bits we use and in which order.  In this case we are only going to use the second part.</li>
  <li><b>Match No</b> - If there are multiple matches, we can specify which match we would like to use.  In this case we are only going to use the first match.</li>
  <li><b>Default Value</b> - This allows us to define the default value to use if we don't get a match for a particular response.  Given the we have put this post processor in the root of our test plan, it will apply to <b>all</b> responses. As such it is highly likely that at some point we won't get a match.  To mitigate this, I am referencing the parameter recursively.  Once it is initialised, it will stay intialised.  In this instance any other default value would be bad as we might find a match in one response, then overwrite it in the next with a default value because we have not found another match.</li>
</ul>
</p>
<p>Part 3 will tie all of this together.  We will cover writing a Groovy script to process the recorded JMeter test plan and replace the original search result URL with the value pulled out using the Regular Expression Extractor.  At that point we should hopefully be in a position to change our automated UI test to perform an entirely different search and record  a new performance test script with (almost!) no manual involvement.</p>
