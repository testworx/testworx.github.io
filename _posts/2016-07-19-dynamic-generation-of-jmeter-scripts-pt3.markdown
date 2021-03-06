---
layout:     post
comments: true
title:      "Auto Generating JMeter Test Plans Pt. 3"
subtitle:   "Post Processing Using Groovy"
date:       2016-07-19 12:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<p>This is part 3 of a series where I am demonstrating how we can auto generate a JMeter test plan using Geb and then automatically perform correlation using a groovy script.  <a href="{% post_url 2016-06-01-dynamic-generation-of-jmeter-scripts-pt1 %}">Part 1</a> focussed on integrating Geb with JMeter so that we could record the browser traffic generated by our automated tests.  <a href="{% post_url 2016-06-27-dynamic-generation-of-jmeter-scripts-pt2 %}">Part 2</a> covered performing some initial correlation to establish what dynamic information we would need to correlate.  Part 3, this article, will cover developing the Groovy script to automatically correlate the JMeter test plan should we need to re-record it or record new ones.</p>

<h2>Git Repository</h2>
<p>A git repository is available containing all the files used in this blog series:  <a href="https://github.com/testworx/jmeter-test-plan-generator">here</a></p>

<h2>Post Processing</h2>
<p>Writing a Groovy script will enable us to programmatically parse the recorded JMeter test plan and replace specific recorded values with variable names from the regular expression extractors we developed in Part 2 of the series.  We build into the script, the ability to parameterise the correct request parameters with variable names for our Regular Expression Extractors.  The Groovy script in its entirety can be found here:  <a href="https://github.com/testworx/jmeter-test-plan-generator/blob/master/src/main/groovy/TestPlanParameteriser.groovy"><i>src/main/groovy/TestPlanParameteriser.groovy</i></a><br><br>Note that when we run this script, we run it from the groovy directory, NOT from the project root (this should probably be wrapped in a Gradle task).  All file paths in the script are relative to this directory.</p>

<h2>Breaking down the script.</h2>
<p>The first steps in this script read in the recorded test plan and the test plan template <i>.jmx</i> files:<br>
  <pre>
      <code>Node test_recording = new XmlParser().parse('../resources/Recording.Template.jmx')<br>
    Node test_template = new XmlParser().parse('../resources/TestPlan.Template.jmx')<br>
    Node test_plan = test_template<br></code>
  </pre>
</p>

<p>The next stage once we have read in the test plan is to iterate through the 'elementProp' nodes and find the 'url' value node.  Once we have found it, we replace the recorded value with a reference to the <b>RESULT_URL</b> variable we created using the Regular Expression Extractor in <a href="{% post_url 2016-06-27-dynamic-generation-of-jmeter-scripts-pt2 %}">Part 2</a>:<br>
  <pre>
    <code>test_recording.'**'.findAll { elementProp ->
        elementProp.@name == 'url' }.each {
        it.'**'.findAll { stringProp ->
            stringProp.@name == 'Argument.value' }.each {
              println 'stringProp: parameterising value: '+it.value()
            it.children()[0] = '${RESULT_URL}'
        }
    }</code>
  </pre>
</p>

<p>The next stage after replacing all the values we wish to replace is to read out the recorded samples from our recorded script, ready to write into a new test plan containing all our post processors.
  <pre>
    <code>Node test_plan_recorded_transactions = test_recording.hashTree.hashTree.'**'.find { node ->
        node.name() == 'RecordingController'
    }.parent()

    //find the hashTree element that actually stores the recorded transactions for the recording controller and return the Transaction Controller elements and associated hashTree elements
    NodeList test_plan_transactions = test_plan_recorded_transactions.'**'.find { node ->
         node.name() == 'hashTree'
     }.children()[1].children()</code>
  </pre>
</p>

<p>Once we have read the samples, the next step is to actually put them into the new test plan.
  <pre>
    <code>test_plan_transactions.each { it ->
        test_plan.hashTree.hashTree.'**'.find { node ->
            node.name() == 'RegexExtractor'
        }.parent().append(it)
    }</code>
  </pre>
</p>

<p>Once the samples have been added to the test plan, we need to write it to a file so we can actually open it with JMeter!
  <pre>
    <code>File testPlan = new File('../../test/jmeter/TestPlan.Parameterised.jmx')

    def printer = new XmlNodePrinter(new PrintWriter(new FileWriter(testPlan)))
    printer.preserveWhitespace = true
    printer.print(test_plan)</code>
  </pre>
</p>

<h2>Putting It All Together</h2>
<p>
  We are ready to record our script as we did in Part 1, then run our groovy script to perform the correlation and then replay, to prove it works.
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
    <li>The test plan is now ready for correlation</li>
    <li>Switch back to the command line and change directory to <i>src/main/groovy</i></li>
    <li>Run <i>groovy TestPlanParameteriser.groovy</i>
    <img src="/assets/img/jmeter_test_plans/parameteriser_output.png" style="width:600px" /></li>
    <li>In JMeter, open <i>src/test/jmeter/TestPlan.Parameterised.jmx</i> and press play.  You should be able to see the test plan run back with no issues, and go to Wikipedia.org.
    <img src="/assets/img/jmeter_test_plans/wikipedia_example.png" style="width:600px" /></li>
  </ol>
</p>

<h2>Summary</h2>
<p>Using a Google search example is rather basic, but when you are doing this on something much more complex e.g. policy management system, large customer ordering system etc, you can quickly generate meaningful performance tests from automated functional tests.  The small upfront investment in writing a script to perform the correlation and creating some test plan templates will rapidly increase your ability to generate JMeter Test Plans quickly.</p>
<p>In terms of maintaining the test scripts you generate to handle changes in the application, don't bother.  Simply maintain your automated tests and just record new JMeter Test Plans when necessary!</p>
