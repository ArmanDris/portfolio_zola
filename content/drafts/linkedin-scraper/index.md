+++
authors = ["Arman Drismir"]
title = "LinkedIn Scraper"
description = "Report with my method and results for maximizing LinkedIn API usage."
date = 2024-01-20
[taxonomies]
tags = ["Python", "API", "Web Scraping"]
[extra]
+++

Using the LinkedIn API to get jobs postings was a bit of an art. To organize my efforts this is the report I made with my method and results.

## Fetch Jobs Report

### Problem:
Dealing with LinkedIn's rate limiting is very hard. It is very unclear what causes rate limiting.

### Solution:
It seems LinkedIn's rate limiting is not influenced much by frequency. Using proxies does not work well since the free proxy lists I was scraping were very poor quality(90% broken). A very effective solution is to handle failures gracefully. I try each request 7 times one after another. This improves request success from ~20% to ~99%. Even if I still fail to fetch a job posting I still don't stop, I just remove the job posting from the list and continue. Another important solution is to clearly differentiate between HTML parsing errors and failed requests. Failed requests are unfixable but failed parsing is. Now I have a ~99% success rate with html parsing which means I do not waste time waiting for a request I will not even be able to use.

### Results:
- 5 job postings went from **~18** seconds to **~4.7**
- Success rate went from **~20%** to **~99%**
- These were accomplished by:
	- Perfecting HTML parsing. **~100%** of requests are successfully parsed
	- Do not hard fail on failed request. Instead try up to 7 times before abandoning request
	- Do not hard fail on abandoned requests. Instead remove job from array and continue