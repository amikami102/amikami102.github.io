---
layout: post
title: Introducing `replicate_warren` project
description: introduction to the project
tags: replicate_warren, current_project, web_scrape, python, R
---

I have a project called `replicate_warren` (a private Github repository), where I am trying to replicate a sociology study by John Robert Warren. Published earlier this year in *Sociological Science* is titled, the study is titled ["How Much Do You Have to Publish to Get a Job in a Top Sociology Department? Or to Get Tenure? Trends over a Generation"](https://www.sociologicalscience.com/articles-v6-7-172/). To put it succinctly, Warren collects data from resumes of sociology PhD graduates and measures how over-achieving recent PhD graduates have to be to land a junior faculty jobs at top sociology departments in the United States. I am trying to replicate the study in the political science field. Due to similar job market structure, I expect to see similar trends in hiring--i.e., higher hurdles for publication numbers and longer post-doc years before first faculty position--but I am also interested in looking at how the trends differ among the four subfields, namely American politics, comparative politics, political theory, and international relations. I have heard rumors about political theory job openings being much lower than other subfields, and it would be valuable to visualize the differences among subfields with data. Second, I want to examine how PhD graduates using qualitative methods are faring in the job market relative to those who heavily use quantitative methods. 

## Method of data collection

Warren selected top 21 sociology departments by triangulating multiple rankings. For these 21 departments, he identified new assistant professors and newly promoted associate professors.[^new] Warren was able to streamline this process by using American Sociological Association's annual guidebook to sociology departments that offer graduate-level degrees and lists faculty members along with their ranks. Unfortunately, the American Political Science Association does not publish such a guidebook.[^APSA] Hence, I will be scraping political science department websites to obtain a similar list of junior-level faculty.

Next, Warren had undergraduate research assistants looked at these faculty members' CVs online, their web pages, and Google Scholar and public databases to identify the kind and number of published work and, for new assistant professors, their career path during the gap years between completing their degree and starting their faculty appointment. This part of the process is similar to how I will go about except that I will be automating the process using Python and will be scraping additional features such as the subfield of specialization and the type of research method used in their published works. 

At the end of the data collection, I expect to collect the following features for each new assistant professors and newly promoted associate professors: 

- `name`: (str) name of the faculty;
- `position`: (factor) assistant or associate
- `yr_graduated`: (int) PhD graduation year;
- `school_graduated`: (factor) school that he/she received PhD;
- `gender`: (factor) male or female;
- `subfield`: (factor) American, comparative, international, or theory;
- `n_publication`: (int) number of publications until landing in assistant position or until promotion to associate position;
- `n_gap_year`: (factor) the number of gap years.


For each publication counted in `n_publication`, I would collect the following attributes:

- `type`: (factor) peer-reviewed article, book chapter, book;
- `journal`: (str) if peer-reveiwed article, the title of the journal;
- `method`: (factor) qualitative, quantitative, or mixed.


[^APSA]: APSA does have a list of political science departments that offer graduate-level degrees. 

[^new]: Since I do not have a roster of faculty members for some fixed year, I will be identifying junior faculty members as they are listed on department websites whenever I access. I will be recording the date on which I accessed the website and storing the raw html as files for the record. 

