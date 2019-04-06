---
layout: post
title: "Introducing 'pizza_to_the_polls' project"
description: introduction to the project
tags: pizza_to_the_polls, scraping_tweets, current_project, web_scape, python, R
---

The inspiration for `pizza_to_the_polls` came during the 2018 midterm election when I came across a Twitter account called [`@PizzaTothePolls`](https://twitter.com/PizzaToThePolls). On November 6, 2018, it was tweeting about delivering pizzas to polling locations across the United States (sample tweet below). 


<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">Pizza 1300 S Grand Ave, Santa Ana, CA 92705, USA?<br>. ∧＿∧ <br> (´･ω･)<br>　 |　⌒Ｙ⌒　/ /<br>　 \ヽ　 ｜　 ﾉ／<br>　　＼ ﾄー🍕ーｲ /<br>　　 ｜ ミ土彡 ｜<br> <br>5 pizzas ABSOLUTELY <br> <br>via <a href="https://t.co/34xXjQiPJc">https://t.co/34xXjQiPJc</a></p>&mdash; Pizza to the Polls (@PizzaToThePolls) <a href="https://twitter.com/PizzaToThePolls/status/1060035393968660480?ref_src=twsrc%5Etfw">November 7, 2018</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


Using Twitter API, I have scraped @PizzaToThePoll's midterm election tweets and parsed out the polling location addresses. (Click on thumbnail below to jump to shiny app.)


[![2018 midterm polling locations]({{site.baseurl}}/img/thumbnail_pizza_to_the_polls.png)](https://asakomikami.shinyapps.io/pizza_to_the_polls/)

In other words, I have a nonrandom sample of polling locations that a.) had long lines and b.) had social media-savvy voters waiting in those lines. Next, I will find other polling locations from state government websites. The descriptive analysis will entail comparison of the demographic features of the communities surrounding the polling locations and whether competitive districts were more likely to have longer polling lines.

## About Pizza to the Polls

According to their website, [Pizza to the Polls](https://polls.pizza/about/) is a 501(c)(4) nonprofit organization that receive social media reports about long polling lines and requests pizzas to be delivered from a local pizza restaurant via pizza delivery website, [Slice](https://slicelife.com/).[^activity] A 501(c)(4) organization may engage in lobbying activity in order to promote the social welfare goal pertaining to the organization. Additionally, it does not lose its tax-exemption status from engaging in electoral campaign activities (i.e. activities influencing electoral results or advocating for a candidate) [so long as those activities remain secondary](https://www.irs.gov/charities-non-profits/other-non-profits/social-welfare-organizations).[^chart] 

Therefore, it is not surprising that there is an obvious partisan flavor to [`@PizzaToThePoll`](https://twitter.com/PizzaToThePolls)'s twitter activity. The account follows left-leaning advocacy groups, jounralists, and activists such as [@AMarchForOurLives](https://twitter.com/AMarch4OurLives) and [@maddow, MSNBC's Rachel Maddow](https://twitter.com/maddow). While it follows Barack Obama's and Hillary Clinton's accounts, it does not follow Donald Trump, Republican elected officials, or right-leaning political pundits. 

[^chart]: [Here](https://www.irs.gov/charities-non-profits/common-tax-law-restrictions-on-activities-of-exempt-organizations) is a helpful chart for comparing tax-exempt organizations published by the IRS(Internal Revenue Survey). 

[^activity]: [This article](https://zapier.com/blog/pizza-to-the-polls-automation/?utm_medium=social&utm_source=twitter) from *zapier* sheds more light on how the organization delivers pizzas. 


## Data Collection

Ideally, I would like to 
<ol>
<li>locate all polling locations that were open in the 2018 midterm election and</li>
<li>dentify which polls had long lines. </li>
</ol>

Both of these tasks are challenging since there is no public database on polling locations. I propose the following next-best alternatives to each of these tasks.

### Task 1

In the United States, polling locations are assigned by residential address, so if one were to look up where to vote, they need to enter their residential address on the state government website search form. My solution is to find residential addresses from white pages and National Address Database published by the U.S. Department of Transportion. These addresses will be entered into their respective state government website's search forms to obtain polling location addresses. 

### Task 2

I define a polling place to have a long line if the next voter who arrives would have to wait for at least an hour until he or she could vote. There is a high chance that such locations will be reported on social media. For instance, if a voter tweets about waiting in a long line through a public account that has GPS enabled, then it is possible to locate the polling location by the tweet's geocoordinates data. Most individual Twitter accounts do not have GPS enabled, so this is unlikely to be fruitful.[^geo]

The other alternative is to google search with the key words: "long lines", "2018 midterm". The search result are news reports about long lines at polling locations during the midterm. Some of these news reports mention specific locations that we could add to our collection. 

[^geo]: Jouranlists using their professional accounts are more likely to have GPS enabled for their tweets. 


