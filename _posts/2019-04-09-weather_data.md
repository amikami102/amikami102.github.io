---
layout: post
title: "Collecting weather data using `rnoaa`, Intro."
description: Tutorial post on how to collect historical weather data using R package, 'rnoaa'.
tags: R
---

There was a coauthored project that required historical weather data for the United States at the county level. Specifically, we wanted to get data on the highest temperature and the precipitation level for a specific 7 day interval in years 2006, 2008, 2010, 2012, 2014, and 2016. We can imagine that the final product would have the following columns:
```
date: (date object in YYYY-MM-DD format)
state: (2 digit FIPS code)
county: (3 digit FIPS code)
precipitation: (float, in tenth of mm)
high_temp: (float, in degrees Celsius)
```

An objective like this actually involves two separate tasks. First is getting the data recorded by weather stations for those dates. Second is to assign new data points to each county using that data. The second task is a process called interpolation.[^extrapolation] 

I will dedicate the series, "Collecting weather data using `rnoaa`" to the first task and will write another series "Interpolating weather data using `arcpy`" to the second task. 

[^interpolation]: Here, it is interpolation rather than extrapolation because we are estimating rainfall and temperature data for those locations between weather stations for the same date. It would be extrapolation if we are trying to predict future weather (i.e. data not yet available) based on the same data. 