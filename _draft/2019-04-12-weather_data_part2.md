---
layout: post
title: "Collecting historical weather data, Part II"
description: Part II to a series on how to collect historical weather data using R package, 'rnoaa' and `arcpy` python package.
tags: python weather_data GIS arcpy
---

`prcp_data` we produced in Part I is not useful in and of itself because it only shows precipitation values recorded by weather stations. Ultimately, we want to be able to say county $i$ on day $t$ had $x_{it}$ level of rainfall measured in tenths of mm. This post will cover how to achieve this using `arcpy` python package from ArcGIS. 

NB:bangbang: [`arcpy`](https://pro.arcgis.com/en/pro-app/arcpy/get-started/what-is-arcpy-.htm) only comes with ArcGIS Desktop, which is a paid software for processing and analyzing GIS data. Besides being a paid service, this is not an ideal tool because a.) ArcGIS Desktop runs on Windows but not Mac OS, and b.) it is a pain in the butt to import `arcpy` module to Anaconda's Spyder.[^spyder] I ended up using the jupyter notebook kernel that came with ArcGIS Pro as my IDE.


I am going to follow the steps documented in Section 2.3 "Kriging Interpolation" of [Cooperman (2017) "Supplementary Materials for “Randomization Inference with Rainfall Data: Using Historical Weather Patterns for Variance Estimation.”](https://doi-org.ezproxy.bu.edu/10.1017/pan.2017.17) We will be using universal Kriging, which is the method used by the original paper that Cooperman (2017) is replicating.[^method] The main steps are 

* TOC
{:toc}

[^method]: There are other interpolation methods such as inverse distance weighing (IDW) that are probably more appropriate for rainfall data. See [Meghan Walter (2013) "Mapping Precipitation: Analysis of GIS Interpolation Techniques"](http://sites.tufts.edu/gis/files/2013/02/Walter_Meghan.pdf); [Noori et al. (2014) "Spatial Estimation of Rainfall Distribution and its Classification in Duhok Governorate Using GIS," *Journal of Water REsource and Protection*](http://dx.doi.org/10.4236/jwarp.2014.62012). 

I will be using the following python modules and setup:
```python
import arcpy
from arcpy import env
from arcpy.sa import *
import os
import re
import requests, zipfile, io
import shutil
from dbfread import DBF
from pandas import DataFrame

# arcpy set up
arcpy.CheckoutExtension("Spatial") # Spatial Analyst license comes with ArcGIS Pro
env.parallelProcessingFactor = "100%"
env.overwriteOutput = True

# directories 
pt_dir = # directory storing your input shapefiles
step1_dir = # directory storing outputs from step 1
step2_dir = # directory storing outputs from step 2
step3_dir = # directory storing outputs from step 3
step4_dir = # directory storing outputs from step 4
```



## Step 1: Reproject shapefile

Section 2.3 "Kriging Interpolation" in Cooperman (2017) begins as follows:
>I used python code for ArcMap to interpolate county values in the following way. I projected the weather station points to the USA Contiguous Equidistant Conic projection with units of meters, which preserves distances and is appropriate for the Kriging process. 

Since my shapefiles, "rainfall*.shp", were projected to NAD 1983 projection in the final step of Part I, I need to reproject the station points to USA Contiguous Equidistant Conic projection. The functions I use are 
- [`arcpy.SpatialReference()`](https://pro.arcgis.com/en/pro-app/arcpy/classes/spatialreference.htm), which creates a spatial reference object with the specified projection, and
-  [`arcpy.Project_management()`](https://pro.arcgis.com/en/pro-app/tool-reference/data-management/project.htm) which creates a shapefile with the new projection. 




```python
pattern = re.compile("^rainfall(?P<date>[0-9]{4}\-[0-9]{2}\-[0-9]{2})\.shp$")
shp_files = [file for file in os.listdir(pt_dir)]

for file in shp_files:
    date = pattern.search(file).group("date")
        
    # reproject the rainfall shapefile
    input = os.path.join(pt_dir, file)
    proj = arcpy.SpatialReference("USA Contiguous Equidistant Conic")
    output = "rainfall" + date + "_reproj"
    arcpy.Project_management(input, os.path.join(step1_dir, output), proj) 
    
    # check that the reprojected shapefile now has meter units 
    pr = arcpy.Describe(os.path.join(step1_dir, output)).spatialReference
    print("Rainfall shapefile for {} has units {}".format(date, pr.linearUnitName))

```

## Step 2: Perform Kriging interpolation

Next, Cooperman (2017) describes how they performed the Kriging interpolation: 

>I ran the Kriging procedure using Universal linear drift with cell size of 4,000 meters squared ...
The default and preferred specification is to create a cell value based on the 12 nearest data points, since not all counties have weather stations in them.

Folllowing this description, I created a Kriging model with [`KrigingModelUniversal()`](https://pro.arcgis.com/en/pro-app/arcpy/spatial-analyst/krigingmodeluniversal-class.htm).

```python
cellsize = 4000 # "cell size of 4000 meters squared"
field = "value" # column name storing precipitation data
kradius = RadiusVariable(12)
kModel = KrigingModelUniversal("LINEARDRIFT")
```


