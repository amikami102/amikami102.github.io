---
layout: post
title: "Collecting weather data using `rnoaa`, Intro."
description: Tutorial post on how to collect historical weather data using R package, 'rnoaa'.
tags: R weather_data rnoaa
---

Suppose we want a historical weather data for the United States at the county level. Specifically, we want data on the daily total precipitation level for a specific 7 day interval in the year 2006. We can imagine that the final dataset would have the following columns:
```
date: (date object in YYYY-MM-DD format)
state: (2 digit FIPS code)
county: (3 digit FIPS code)
precipitation: (float, in tenth of mm)
```

An objective like this can be broken into two separate tasks. First is to obtain the data recorded by weather stations for the dates we are intersted. Second is to assign new data points to each county using that data. The second task is a process called interpolation.[^extrapolation] 

I will explain the first task in the blog post, ["Collecting weather data using `rnoaa`, Part I"]({{ site.baseurl }}{% post_url 2019-04-10-weather_data_part1 %}) and the second task in " ..., Part II". 

[^extrapolation]: Here, it is interpolation rather than extrapolation because we are estimating rainfall and temperature data for those locations between weather stations for the same date. It would be extrapolation if we are trying to predict future weather (i.e. data not yet available) based on the same data. 