---
layout: post
title: "Collecting weather data using `rnoaa`, Task 1.1."
description: Tutorial post on how to collect historical weather data using R package, 'rnoaa'.
tags: R weather_data rnoaa
---

As stated in the introductory post, we want precipitation and highest temperature recorded by weather stations in the United States for a 7-day interval in years 2006, 2008, 2010, 2012, 2014, and 2016. 

There is a public database published by National Oceanic Atmospheric Administration (NOAA) called Global Historical Climate Network Daily (GHCN-Daily). Here is the opening paragraph of the description from the [official website](https://www.ncdc.noaa.gov/ghcn-daily-description):
> GHCN (Global Historical Climatology Network)-Daily is an integrated database of daily climate summaries from land surface stations across the globe. Like its monthly counterpart (GHCN-Monthly) , GHCN-Daily is comprised of daily climate records from numerous sources that have been integrated and subjected to a common suite of quality assurance reviews.

GHCN-Daily is the database we want to access. NOAA has set up API to facilitate user itneraction with their database. I accessed the NOAA API using R package, [`rnoaa`](https://CRAN.R-project.org/package=rnoaa). 