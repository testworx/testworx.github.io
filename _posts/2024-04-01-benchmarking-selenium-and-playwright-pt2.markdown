---
layout:     post
comments: true
title:      "Benchmarking Selenium and Playwright Pt. 2"
subtitle:   "Improving browser startup/close times in Selenium"
date:       2024-04-01 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/grafana.png"
---

### Introduction
In [Part 1](https://blog.testworx.io/2024/03/26/benchmarking-selenium-and-playwright/), I did some intial measurments to compare the difference in speed between Selenium and Playwright.  

One of the things that stood out was the difference in time to provision a browser.  Playwright took approximately 250ms 
whilst Selenium took approximately 1.2 seconds.  

I was keen to see if this difference could be reduced.

### Source Code
All source code for this post can be found in [GitHub](https://github.com/testworx/browser-automation-benchmarking/tree/benchmarking-pt-2).
See the README for instructions on how to run the tests.

### Methodology
1. Create shared ChromeDriver service.
2. Create Selenium browser implementation that uses a shared ChromeDriver service.
3. Run tests again to compare the difference in browser startup/close time.

#### Creating and using the shared ChromeDriver service
I created a class **ChromeDriverServiceManager**

The constructor creates a ChromeDriverService instance.

```java
private ChromeDriverServiceManager() {
        MetricsRegistry.getInstance().timer("chromeDriverService.init", "framework", "selenium").record(() -> {
            service = new ChromeDriverService.Builder()
                    .usingAnyFreePort()
                    .build();
        });
    }
```

#### Using the shared service
I updated the SeleniumBrowser class to use the shared ChromeDriver service.  Note we now need to use RemoteWebdriver.
```java
browser = new RemoteWebDriver(ChromeDriverServiceManager.getInstance().service.getUrl(), new ChromeOptions());
```

### Results
#### Browser Lifecycle
![time-to-open-browser.png](/assets/img/2024/april/benchmarks-pt-2/time-to-open-browser.png)

By reusing the ChromeDriver service, the time to open a browser has been reduced to approximately 500ms.

For comparison, starting up a browser using a default ChromeDriver service takes approximately 1.2 seconds.

Playwright is still faster at 250ms, but the gap can be narrowed quite easily.

#### Time to Close Browser
![time-to-close-browser.png](/assets/img/2024/april/benchmarks-pt-2/time-to-close-browser.png)

### Conclusion and next steps
I have demonstrated a simple way of improving browser startup times using Selenium.  It is worth noting that this applies when using a local driver.  If you are running on something like Sauce Labs or Browser Stack, this won't apply.

In the next post I will investigate running the selenium tests in headless mode.  I expect this will improve run times as we no longer need to render the page to the screen, but measuring this should prove interesting.





