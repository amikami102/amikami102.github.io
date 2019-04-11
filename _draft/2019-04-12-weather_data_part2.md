---
layout: post
mathjax: true
title: "Collecting historical weather data, Part II."
description: Part II to a series on how to collect historical weather data using R package, 'rnoaa' and `arcpy` python package.
tags: python weather_data GIS arcpy
---

The data we obtained in Part I is not useful in and of itself because we only have precipitation values recorded by weather stations. Ultimately, we want to be able to say county $i$ on day $t$ had $x_{it}$ level of rainfall measured in tenths of mm. This post will cover how to achieve this using `arcpy` python package from ArcGIS. 

NB: `arcpy` comes with ArcGIS, which is a paid software for processing and analyzing GIS data. In the future, I will write another post that will do interpolation using R package that is available on CRAN. :bangbang: