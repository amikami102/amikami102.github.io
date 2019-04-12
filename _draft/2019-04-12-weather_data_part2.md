---
layout: post
title: "Collecting historical weather data, Part II."
description: Part II to a series on how to collect historical weather data using R package, 'rnoaa' and `arcpy` python package.
tags: python weather_data GIS arcpy
---

`prcp_data` we produced in Part I is not useful in and of itself because it only shows precipitation values recorded by weather stations. Ultimately, we want to be able to say county $i$ on day $t$ had $x_{it}$ level of rainfall measured in tenths of mm. This post will cover how to achieve this using `arcpy` python package from ArcGIS. 

NB:bangbang: [`arcpy`](https://pro.arcgis.com/en/pro-app/arcpy/get-started/what-is-arcpy-.htm) only comes with ArcGIS Desktop, which is a paid software for processing and analyzing GIS data. Besides being a paid service, this is not an ideal tool because a.) ArcGIS Desktop runs on Windows but not Mac OS, and b.) it is a pain in the butt to import `arcpy` module to Anaconda's Spyder.[^spyder] I ended up using the jupyter notebook kernel that came with ArcGIS Pro as my IDE.


I am going to follow the steps documented in [Cooperman (2017) "Supplementary Materials for “Randomization Inference with Rainfall Data: Using Historical Weather Patterns for Variance Estimation.”](https://doi-org.ezproxy.bu.edu/10.1017/pan.2017.17) We will be using universal Kriging, which is the method used by the original paper that Cooperman (2017) is replicating.[^method] The main steps are 

* TOC
{:toc}

[^method]: There are other interpolation methods such as inverse distance weighing (IDW) that are probably more appropriate for rainfall data. See [Meghan Walter (2013) "Mapping Precipitation: Analysis of GIS Interpolation Techniques"](http://sites.tufts.edu/gis/files/2013/02/Walter_Meghan.pdf); [Noori et al. (2014) "Spatial Estimation of Rainfall Distribution and its Classification in Duhok Governorate Using GIS," *Journal of Water REsource and Protection*](http://dx.doi.org/10.4236/jwarp.2014.62012). 



