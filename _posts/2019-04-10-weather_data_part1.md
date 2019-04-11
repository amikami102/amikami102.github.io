---
layout: post
title: "Collecting weather data using `rnoaa` package, Part I"
description: Tutorial post on how to collect historical weather data using R package, 'rnoaa'.
tags: R weather_data rnoaa
---

As stated in the [introductory post]({{ site.baseurl }}{% post_url 2019-04-09-weather_data %}), we want the daily total precipitation level recorded by weather stations in the United States for a 7-day interval in the year 2006, specifically from November 1st to Novebmer 7th, 2006.

There is a public database published by National Oceanic Atmospheric Administration (NOAA) called Global Historical Climate Network Daily (GHCN-Daily). Here is the opening paragraph of the description from the [official website](https://www.ncdc.noaa.gov/ghcn-daily-description):

> GHCN (Global Historical Climatology Network)-Daily is an integrated database of daily climate summaries from land surface stations across the globe. Like its monthly counterpart (GHCN-Monthly), GHCN-Daily is comprised of daily climate records from numerous sources that have been integrated and subjected to a common suite of quality assurance reviews.

So, it sounds like GHCN-Daily is exactly the database we want to access. Thankfully, NOAA has set up an API (Application Programming Interface) to facilitate user interaction with their database. We will use `rnoaa` package to interact with the NOAA API, which in turn will make requests to the NOAA data server. The overall steps are as follows:

1. [obtain NOAA API key token](#Step1);
2. [obtain weather data with `rnoaa::ncdc()`](#Step2);
3. [obtain weather station data with `rnoaa::ncdc_stations()`](#Step3);
4. [join the data obtain from step 2 to that from step 3.](#Step4)

## Step 1

After getting an [API key token](https://www.ncdc.noaa.gov/cdo-web/token), I wrote the key in my ".Renviron" file located in my home directory.[^key] Write the following line in your command line, substituting `<your-api-key>` with your NOAA API key token.

```
$ echo 'NOAA_KEY = <your-api-key>' > .Renviron
```
[^key]: For how to store an API key, I referred to [Section 2.4 "R Startup"](https://csgillespie.github.io/efficientR/r-startup.html#r-startup-arguments) in *Efficient R Programming* by Gillepsie and Lovelace.


## Step 2

Let's set up the libraries and API key. 
```r
Sys.getenv("NOAA_KEY") # your NOAA API key
library(rnoaa)
library(tidyverse)
library(magrittr)
library(logging)
```

And, I will also set a vector of dates I need.
```r
days <- seq(from = as.Date("2006-11-01"), 
            to = as.Date("2006-11-07"), by = "day")
```

To start requesting data, I need to specify which data I want in the language that the server would understand. This is done by filling out the pertinent arguments in the function, `rnoaa:ncdc()`. Note that many of the arguments shown below are deprecated as of version 0.8.4.
```r
ncdc(datasetid = NULL, datatypeid = NULL, stationid = NULL,
    locationid = NULL, startdate = NULL, enddate = NULL,
    sortfield = NULL, sortorder = NULL, limit = 25, offset = NULL,
    token = NULL, dataset = NULL, datatype = NULL, station = NULL,
    location = NULL, locationtype = NULL, page = NULL, year = NULL,
    month = NULL, day = NULL, includemetadata = TRUE, results = NULL,
    add_units = FALSE, ...)
```
I will be using the following six arguments:

- `datasetid = "GHCND"` to access GHCN-Daily,
- `datatypeid = "PRCP"` to get the precipitation level,[^datatype]
- `startdate` and `enddate` to specify the date I want the data for,
- `limit = 1000` to set the number of results per request to 1000 (the maximum),
- `offset` to specify which entry to start displaying. 

[^datatype]: Consult [the documentation](https://www1.ncdc.noaa.gov/pub/data/ghcn/daily/readme.txt) from NOAA website for more information on available data types for GHCND-Daily.

Here is the code for the function I wrote to retrieve precipitation data for a specific day. I will breakdown the code into steps and explain them one by one. 

```r
get_prcp <- function(day, datatype){
                #------------------
                # Retrieves the precipitation data for 
                # a specific day.
                #-------------------
                # day (date, YYYY-MM-DD format)
                #--------------------

                # create an empty dataframe
                df <- data.frame(station = character(), 
                                 value = integer(), 
                                 stringsAsFactors=FALSE) 
                # The maximum number of results displayed for each 
                # request is 1000. We want to count how many requests
                # are necessary to get all the results. 
                counter <- ncdc(datasetid = "GHCND", 
                                datatypeid =  "PRCP",
                                startdate = as.character(day),
                                enddate = as.character(day))$meta$totalCount %/% 1000
                for(i in 1:counter){
                    safe_fun <- safely(ncdc) 
                    response <- safe_fun(datasetid = "GHCND",
                        datatypeid = "PRCP",
                        startdate = as.character(day),
                        enddate = as.character(day),
                        limit = 1000,
                        offset = 1000*i)
                    df <- response$result$data %>%
                            select(station, value) %>% rbind(df)
                    # print error message if not NULL
                    if(!is.null(response$error)){loginfo(response$error)}
                }
                return(df)
        }
```

First, I set up an empty dataframe with columns "station" and "value". Each row records the precipitation level recorded by the weather station on `day`.
```r
df <- data.frame(station = character(), 
                value = integer(), 
                stringsAsFactors = FALSE) 
```

Second, I set up an object called `counter`. Because I will not retrieve all the data I want in a single request, I want to look at the metadata to see how many requests are necessary if the maximum number of results per request is 1000. `ncdc()` returns a list of two lists, `meta` and `data`. Inside the `meta` sublist is an element called `totalCount`, which I mod by 1000 to get the number of requests I need to make. 
```r
counter <- ncdc(datasetid = "GHCND", 
                datatypeid =  "PRCP", 
                startdate = as.character(day),
                enddate = as.character(day))$meta$totalCount %/% 1000
```

The final component of `get_prcp` is this `for` loop. To catch exceptions caused by bad requests or missing data, I first create `safe_fun` by wrapping `ncdc()` with `safely()` function from `purrr` package. It can be thought of as a safe version of ncdc that will  handle errors without stopping the function from running. `safe_fun` will return a list with two lists, `result` and `error`. The `result` sublist contains the usual output of `ncdc()` while the `error` sublist contains the error messages if an error occurs and `NULL` otherwise.
```r
for(i in 1:counter){
    safe_fun <- safely(ncdc) 
    response <- safe_fun(datasetid = "GHCND",
                        datatypeid = "PRCP",
                        startdate = as.character(day),
                        enddate = as.character(day),
                        limit = 1000,
                        offset = 1000*i)
    df <- response$result$data %>%
            select(station, value) %>% 
            rbind(df)
    # print error message if not NULL
    if(!is.null(response$error)){loginfo(response$error)}
}
```

Now, let's map `get_prcp` to `days`. The object `prcp_data` is a list of 7 elements where each element is a dataframe with two columns, "station" and "value". 
```r
prcp_data <- days %>% 
                set_names(as.character(.)) %>% 
                map(~ get_prcp(.x))
glimpse(prcp_data)
```

```
List of 7
 $ 2006-11-01:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11304 obs. of  2 variables:
  ..$ station: chr [1:11304] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam004" "GHCND:US10adam006" ...
  ..$ value  : int [1:11304] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-02:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11288 obs. of  2 variables:
  ..$ station: chr [1:11288] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam003" "GHCND:US10adam004" ...
  ..$ value  : int [1:11288] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-03:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11251 obs. of  2 variables:
  ..$ station: chr [1:11251] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam003" "GHCND:US10adam004" ...
  ..$ value  : int [1:11251] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-04:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11145 obs. of  2 variables:
  ..$ station: chr [1:11145] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam004" "GHCND:US10adam006" ...
  ..$ value  : int [1:11145] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-05:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11136 obs. of  2 variables:
  ..$ station: chr [1:11136] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam003" "GHCND:US10adam004" ...
  ..$ value  : int [1:11136] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-06:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11212 obs. of  2 variables:
  ..$ station: chr [1:11212] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam003" "GHCND:US10adam004" ...
  ..$ value  : int [1:11212] 0 0 0 0 0 0 0 0 0 0 ...
 $ 2006-11-07:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	11253 obs. of  2 variables:
  ..$ station: chr [1:11253] "GHCND:US10adam001" "GHCND:US10adam002" "GHCND:US10adam003" "GHCND:US10adam004" ...
  ..$ value  : int [1:11253] 0 0 0 0 0 0 0 0 0 0 ...
```

## Step 3

Next, we want to get more information on the weather stations, specifically their geographical coordinates. We will use the function, `rnoaa::ncdc_stations()`. (Again, many of these arguments are deprecated as of `rnoaa` version 0.8.4.)
```r
ncdc_stations(stationid = NULL, datasetid = NULL, datatypeid = NULL,
  locationid = NULL, startdate = NULL, enddate = NULL,
  sortfield = NULL, sortorder = NULL, limit = 25, offset = NULL,
  datacategoryid = NULL, extent = NULL, token = NULL,
  dataset = NULL, station = NULL, location = NULL,
  locationtype = NULL, page = NULL, ...)
```
Similar to the arguments for `ncdc()`, I will be using the following arguments:

- `datasetid = "GHCND"`,
- `datatypeid = "PRCP"`,
- `startdate = "2006-11-01` and `enddate = "2006-11-07"` because I only want weather stations whose data recorded from 2006-11-01 to 2006-11-07 are available,
- `limit = 1000`,
- `offset`. 

The following function retrieves information on weather stations. Note that its structure and steps are very similar to `get_prcp`. 
```r
get_stations <- function(start, end){
        #--------------------------------
        # Retrieve the geographical coordinates of
        # weather stations in the US whose data is available
        # for the observation period. 
        # Output is a dataframe with three columns: 
        # the station id, longitude, and latitude. 
        #-------------------------------
        # state (chr, start date in YYYY-MM-DD format)
        # end (chr, end date in YYYY-MM-DD format)
        #--------------------------------
        
        # count how many API requests are necessary to get all the results
        counter <- ncdc_stations(datasetid = "GHCND", 
                              dattatypeid = "PRCP",
                              startdate = start, 
                              enddate = end,
                              limit = 1000)$meta$totalCount %/% 1000
        # create empty dataframe to store output
        df <- data.frame(station = character(),
                         longitude = numeric(),
                         latitude = numeric())
        for (i in 1:counter){
                safe_fun <- safely(ncdc_stations)
                response <- safe_fun(datasetid = "GHCND",
                                datatypid = "PRCP",
                                startdate = start,
                                enddate = end, 
                                limit = 1000,
                                offset = 1000*i)
                df <- response$result$data %>%
                        select(id, longitude, latitude) %>%
                        filter(grepl("GHCND:US", id)) %>%
                        rbind(df)
                if(!is.null(response$error)){loginfo(response$error)}
        }
      df %<>% filter(!duplicated(df)) # remove duplicates
      return(df)
}
```

```r
stations <- days[c(1, 7)] %>% map2(~ get_stations(.x, .y))
glimpse(stations)
```

```
Observations: 416
Variables: 3
$ id        <chr> "GHCND:USW00026523", "GHCND:USW00026…
$ longitude <dbl> -151.2391, -150.0950, -152.1067, -15…
$ latitude  <dbl> 60.57970, 62.32000, 65.17500, 66.916…
```

## Step 4

Let's go over the two list objects, `prcp_data` and `stations`. `prcp_data` is a list of 7 dataframes, each dataframe recording the precipitation level recorded by weather stations in the United States on one 7 days listed in `days`. `stations` is a dataframe of weather stations with their longitude and latitude. Naturally, we want to join `stations` to each dataframe in `prcp_data` by the station id.

```r
prcp_data %<>% map(~{
        left_join(.x, stations, by = c("station" = "id")) %>%
                drop_na(longitude, latitude)
})
glimpse(prcp_data)
```

```
List of 7
 $ 2006-11-01:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	400 obs. of  4 variables:
  ..$ station  : chr [1:400] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:400] 0 0 0 0 0 8 0 18 3 0 ...
  ..$ longitude: num [1:400] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:400] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-02:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	399 obs. of  4 variables:
  ..$ station  : chr [1:399] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:399] 0 0 0 0 0 0 0 3 0 5 ...
  ..$ longitude: num [1:399] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:399] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-03:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	399 obs. of  4 variables:
  ..$ station  : chr [1:399] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:399] 0 0 0 0 5 0 23 0 0 0 ...
  ..$ longitude: num [1:399] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:399] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-04:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	399 obs. of  4 variables:
  ..$ station  : chr [1:399] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:399] 0 0 0 0 3 0 0 0 0 0 ...
  ..$ longitude: num [1:399] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:399] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-05:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	399 obs. of  4 variables:
  ..$ station  : chr [1:399] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:399] 0 0 0 0 0 3 0 13 5 0 ...
  ..$ longitude: num [1:399] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:399] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-06:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	401 obs. of  4 variables:
  ..$ station  : chr [1:401] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:401] 0 0 0 8 0 0 0 3 5 0 ...
  ..$ longitude: num [1:401] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:401] 60.6 62.3 65.2 66.9 60.8 ...
 $ 2006-11-07:Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	401 obs. of  4 variables:
  ..$ station  : chr [1:401] "GHCND:USW00026523" "GHCND:USW00026528" "GHCND:USW00026529" "GHCND:USW00026533" ...
  ..$ value    : int [1:401] 0 0 0 0 0 0 0 0 0 0 ...
  ..$ longitude: num [1:401] -151 -150 -152 -152 -162 ...
  ..$ latitude : num [1:401] 60.6 62.3 65.2 66.9 60.8 ...
```

This completes the task of collecting daily precipitation data recorded by weather stations in the United States betwen November 1st and November 7th, 2006. In Part II, we will use Kriging interpolation on this data to obtain precipitation data for each county. 
```r
export(prcp_data, "prcp_data.rds")
```

