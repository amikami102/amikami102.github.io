---
layout: post
mathjax: true
title: "Collecting historical weather data, Part II."
description: Part II to a series on how to collect historical weather data using R package, 'rnoaa' and `arcpy` python package.
tags: python weather_data GIS arcpy
---

Part II will be covering interpolation. The data we obtained in Part I is not useful in and of itself because it is basically precipitation values recorded by weather stations. Ultimately, we want to be able to say county $i$ on day $t$ had $x_{it}$ level of rainfall measured in tenths of mm. To do this,