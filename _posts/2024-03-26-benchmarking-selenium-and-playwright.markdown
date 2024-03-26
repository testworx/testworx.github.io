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
#### Browser Startup

#### Navigate to URL

#### Find Element on the Page

#### Click on an Element

#### Type Text into an Element

### Observations

### Conclusion

