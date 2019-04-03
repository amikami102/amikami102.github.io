---
layout: post
title: "Introducing 'pizza_to_the_polls' project"
---

'pizza_to_the_polls' is a big data collection project that I've started working on in February 2019. The inspiration came during the 2018 midterm election when I came across a Twitter account called [`@PizzaTothePolls`](https://twitter.com/PizzaToThePolls). On November 6, 2018, it was tweeting about delivering pizzas to polling locations across the United States (sample tweet below). 

{% twitter https://twitter.com/PizzaToThePolls/status/1060035393968660480 %}

Using Twitter API, I have scraped their midterm election tweets and parsed out the polling location addresses. In other words, I have a nonrandom sample of polling locations that a.) had long lines but b.) also had social media-savvy voters waiting in those lines. My goal is to merge compare these polling locations with other polling locations to evaluate how local governments are administering electoral process. In the United States, the party that controls state legislatures tend to have overwhelming opportunity to manipulate the electoral process in their favor through redistricting, legislations, and administration. 

## About Pizza to the Polls

According to their website, [Pizza to the Polls](https://polls.pizza/about/) is a 501(c)(4) nonprofit organization that receive social media reports about long polling lines and requests pizzas to be delivered from a local pizza restaurant via pizza delivery website, [Slice](https://slicelife.com/).[^activity] Like 501(c)(3), a 501(c)(4) organization may engage in lobbying activity in order to promote the social welfare goal pertaining to the organization. Additionally, unlike 501(c)(3)'s, 501(c)(4)'s do not lose their status for engaging in electoral campaign activities (i.e. activities influencing electoral results or advocating for a candidate) [so long as they remain secondary](https://www.irs.gov/charities-non-profits/other-non-profits/social-welfare-organizations).[^chart] 

In accordance with these exemption requirements, there is an obvious partisan flavor to [`@PizzaToThePoll`]((https://twitter.com/PizzaToThePolls)'s twitter timeline. The organization's Twitter account follows left-leaning advocacy groups and activists such as [@AMarchForOurLives](https://twitter.com/AMarch4OurLives) and recognizably left-leaning pundits such as [@maddow, MSNBC's Rachel Maddow](https://twitter.com/maddow). While it follows [@POTUS44](https://twitter.com/POTUS44) and [@HillaryClinton](https://twitter.com/HillaryClinton), it does not follow [@realDonaldTrump](https://twitter.com/realDonaldTrump), Republican elected officials, or right-leaning political pundits. 

[^chart]: [Here](https://www.irs.gov/charities-non-profits/common-tax-law-restrictions-on-activities-of-exempt-organizations) is a helpful chart for comparing tax-exempt organizations published by the IRS(Internal Revenue Survey). 

[^activity]: [This article](https://zapier.com/blog/pizza-to-the-polls-automation/?utm_medium=social&utm_source=twitter) from *zapier* sheds more light on how the organization delivers pizzas. 



