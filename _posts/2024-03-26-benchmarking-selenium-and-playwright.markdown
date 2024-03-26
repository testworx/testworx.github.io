---
layout:     post
comments: true
title:      "Benchmarking Selenium and Playwright"
subtitle:   "Understand the differences"
date:       2024-03-26 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

### Introduction
I recently had a discussion with a QA colleague about why we might use Playwright over Selenium.  I am a big fan of Selenium, 
and for me, the biggest selling point is the W3C Webdriver standard it pioneered.  This means that any browser that supports
the W3C Webdriver standard can be automated with Selenium.  Playwright, a newer popular automation library, has many fans 
who champion it's out of the box advantages of speed and reliability.  

These advantages seemed like something worth measuring, to understand where Playwright  might be better, and how users of 
Selenium update their tests frameworks to address some of the relative shortcomings.

### Source Code
All source code for this post can be found in [GitHub](https://github.com/testworx/browser-automation-benchmarking/tree/benchmarking-pt-1).
See the README for instructions on how to run the tests.

### Methodology
1. To accurately compare each tool, I created an abstraction layer that provides a common interface for writing a test.
2. I created Broswer implementations for both Selenium & Playwright that implement the common interface.
3. I created a simple test that opens a browser, navigates to https://automationteststore.com and interacts with the web app.
4. For each action, I measured the time it took to complete the action and recorded the metrics in Prometheus.
5. I ran the tests 100 times for each browser implementation and recorded the results.
6. I created a dashboard to more easily visualise these metrics.

### Results
#### Browser Lifecycle
![browser-lifecycle.png](/img/2024/march/benchmarks-pt-1/browser-lifecycle.png)

#### Navigate to URL
![browser-navigation.png](/img/2024/march/benchmarks-pt-1/browser-navigation.png)

#### Find Element on the Page
![find-element.png](/img/2024/march/benchmarks-pt-1/find-element.png)

#### Click on an Element
![click-element.png](/img/2024/march/benchmarks-pt-1/click-element.png)

#### Type Text into an Element
![type-in-element.png](/img/2024/march/benchmarks-pt-1/type-in-element.png)

#### Test Duration
![test-duration.png](/img/2024/march/benchmarks-pt-1/test-duration.png)

### Observations
Opening a browser and navigation are significantly faster in Playwright.  The difference in these to actions seems to 
correlate with the speed difference when comparing the overall test duration.

Having said that, other actions are generally faster too.  When running the tests, Playwright tests ran in a headless chrome 
instance by default whereas Selenium would open a browser window.  This could account for some of the speed differences 
as Selenium must wait for the page to render.

### Conclusion and next steps
Out of the box, Playwright is faster than Selenium in the context of the test I have written.  On a longer test, with 
more backend processing, I suspect the difference would be less pronounced, but with the current setup, the tests run 
significantly faster.

Playwright tests seem to run with a headless version of Chrome by default.  I am inclined to investigate if running the 
Selenium tests in headless mode would improve performance.

I would like to investigate if managing the ChromeDriver service manually would improve browser startup.  I suspect it might.

I haven't really looked at the reliability of the tests.  When I ran the tests 1000 times I did notice an approvimate 0.4% 
failure rate in the Selenium tests.  I would like to investigate this further.

One thing I did notice is that Playwright downloads its own browser binaries on first run. 
This could mean that the Selenium and Playwright tests were running on different browser versions and could account for 
timing differences.

So, I have a few things to investigate in the next post; reliability, browser startup and headless mode.  

Stay tuned...




