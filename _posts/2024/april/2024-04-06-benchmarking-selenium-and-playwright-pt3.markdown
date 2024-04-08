---
layout:     post
comments: true
title:      "Benchmarking Selenium and Playwright Pt. 3"
subtitle:   "The impact of running Chrome in headless mode"
date:       2024-04-06 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/grafana.png"
---

### Introduction
In [Part 2](https://blog.testworx.io/2024/04/01/benchmarking-selenium-and-playwright-pt2/), I investigated how managing the ChromeDriver service can improve browser startup/close times in Selenium.  

The overall conclusion was that browser startup could be improved by reusing a ChromeDriver service, but overall test runtime was still significantly slower than the equivalent Playwright test.

I was keen to see if this difference could be reduced by running Chrome in headless mode.

### Source Code
All source code for this post can be found in [GitHub](https://github.com/testworx/browser-automation-benchmarking/tree/benchmarking-pt-3).
See the README for instructions on how to run the tests.

### Methodology
1. Extend SeleniumBrowser class to add an option for running Chrome in Headless mode using a managed service.
2. Run tests again to compare the difference in browser startup time and overall test runtime

#### Extend SeleniumBrowser class to run Chrome in Headless mode

I extended the **Open()** method to add another config option

```java
 case "chrome_headless_with_managed_service":
    ChromeOptions options = new ChromeOptions();
    options.addArguments("--headless=new");
    browser = new RemoteWebDriver(ChromeDriverServiceManager.getInstance().service.getUrl(), options);
    break;
```

### Test Results
#### Avg. Time to Open Browser
![avg-time-to-open-browser.png](/assets/img/2024/april/benchmarks-pt-3/avg-time-to-open-browser.png)

By reusing the ChromeDriver service, the time to open a browser was reduced by 30%. Starting in headless mode as well, reduced startup time by a further 30%.

With very little effort we were able to reduce startup time by almost 60%!

Playwright is still 2x faster at 146ms.

#### Avg. Time to Click element
![avg-time-to-click-element.png](/assets/img/2024/april/benchmarks-pt-3/avg-time-to-click-element.png)

As in the other test runs, Playwright is fastest.  Compared to a default Chrome instance, Headless mode reduces the time to click an element by ~10%.

#### Avg. Test Runtime
![avg-test-runtime.png](/assets/img/2024/april/benchmarks-pt-3/avg-test-runtime.png)

### Conclusion
Compared to using Chrome with a default Chromedriver, we can say the following:
- Starting Chrome in headless mode, using an existing ChromeDriver service, reduces the time to open a browser by approximately 50%.
- Element interactions are approximately 10% faster.
- Overall test runtime is approximately 20% faster.

Compared to Playwright, we can say the following:
- It still takes over 2x as long to open a browser (Playwright doesn't really open a "new" browser).
- Clicking an element is almost 1.8x slower.
- Overall test runtime is approximately 25% slower.

The test used to measure these differences was very short and simple but it gives a sense of some of the relative differences in performance between the two tools.
I think a longer test, with more backend processing is likely to reduce the overall gap, but all things being equal, Playwright is still the faster tool out of the box.

This is a fairly large subject to investigate and I am not sure whether to do another blog post at the moment.  
Things I think could be worth investigating further in the future are:
- Changing the page loading strategy
- Increasing the polling rate for the DOM

#### Final Thoughts
This wasn't really a review of Playwright.  It was an effort to investigate performance differences with Selenium and to try and reduce the gap.

Having said that, from the small amount of time I have spent with Playwright, it seems good.  Tests run faster, and seem more reliable.  If you are already using Selenium, I don't see a compelling reason to change and rewrite all your tests.  If you are starting from scratch, you have nothing to lose by looking at Playwright.

As always, an automation framework is more than just the libraries you use to open a browser or talk to an API, so bare that in mind when choosing a tool and look at the bigger picture.




