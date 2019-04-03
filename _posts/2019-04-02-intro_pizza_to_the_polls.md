---
layout: post
title: "Introducing 'pizza_to_the_polls' project"
---

'pizza_to_the_polls' is a big data collection project that I've started working on in February 2019. The inspiration came during the 2018 midterm election when I came across a Twitter account called [`@PizzaTothePolls`](https://twitter.com/PizzaToThePolls). On November 6, 2018, it was tweeting about delivering pizzas to polling locations across the United States (sample tweet below). 

{% twitter https://twitter.com/PizzaToThePolls/status/1060035393968660480 %}

Using Twitter API, I have scraped their midterm election tweets and parsed out the polling location addresses. In other words, I have a nonrandom sample of polling locations that a.) had long lines but b.) also had social media-savvy voters waiting in those lines. It provides a snapshot of young voters 

My goal is to merge compare these polling locations with other polling locations to evaluate how local governments are administering electoral process.

## About Pizza to the Polls

According to their website, [Pizza to the Polls](https://polls.pizza/about/) is a 501(c)(4) nonprofit organization that receive social media reports about long polling lines and requests pizzas to be delivered from a local pizza restaurant via pizza delivery website, [Slice](https://slicelife.com/). Like 501(c)(3), a 501(c)(4) organization may engage in lobbying activity in order to promote the social welfare goal pertaining to the organization. Unlike 501(c)(3), however, 501(c)(4) do not lose their status for engaging in electoral campaign activities (i.e. activities influencing electoral results or advocating for a candidate) [so long as they remain secondary](https://www.irs.gov/charities-non-profits/other-non-profits/social-welfare-organizations).[^chart] 

[^chart]: [Here](https://www.irs.gov/charities-non-profits/common-tax-law-restrictions-on-activities-of-exempt-organizations) is a helpful chart for comparing tax-exempt organizations published by the IRS(Internal Revenue Survey). 


## Data Collection 