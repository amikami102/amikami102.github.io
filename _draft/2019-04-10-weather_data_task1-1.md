---
layout: post
title: "Collecting weather data using `rnoaa`, Task 1.1."
description: Tutorial post on how to collect historical weather data using R package, 'rnoaa'.
tags: R weather_data rnoaa
---

As stated in the introductory post, we want daily precipitation and highest temperature recorded by weather stations in the United States for a 7-day interval in years 2006, 2008, 2010, 2012, 2014, and 2016. 

There is a public database published by National Oceanic Atmospheric Administration (NOAA) called Global Historical Climate Network Daily (GHCN-Daily). Here is the opening paragraph of the description from the [official website](https://www.ncdc.noaa.gov/ghcn-daily-description):
> GHCN (Global Historical Climatology Network)-Daily is an integrated database of daily climate summaries from land surface stations across the globe. Like its monthly counterpart (GHCN-Monthly) , GHCN-Daily is comprised of daily climate records from numerous sources that have been integrated and subjected to a common suite of quality assurance reviews.

So, it sounds like GHCN-Daily is the database we want to access. NOAA has set up API to facilitate user itneraction with their database. We will use `rnoaa` package to interact with the NOAA API, which in turn will make requests to the NOAA data server. The overall steps are as follows:

1. obtain NOAA API key token;
2. obtain weather data with `rnoaa::ncdc()`;
3. obtain weather station data with `rnoaa::ncdc_stations()`;
4. join the data obtain from step 2 to that from step 3.

## Obtain and store NOAA API key token

After getting an [API key token](https://www.ncdc.noaa.gov/cdo-web/token), I wrote the key in my ".Renviron" file located in my home directory.[^key] Write the following line in your command line, substituting `<your-api-key>` with your NOAA API key token.

```
echo 'NOAA_KEY = <your-api-key>' > .Renviron
```
Then when I use `rnoaa` functions in RStudio, I include the following line in the beginning of my script to avoid feeding the API key every time. 
```{R}
Sys.getenv("NOAA_KEY")
```


## `ncdc()` to obtain weather data 


## `ncdc_stations()` to obtain weather station coordinates

[^key]: For how to store an API key, I referred to [Section 2.4 "R Startup"](https://csgillespie.github.io/efficientR/r-startup.html#r-startup-arguments) in *Efficient R Programming* by Gillepsie and Lovelace.
