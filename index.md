---
title       : Twitter & Trauma Care
subtitle    : Using social media to inform trauma center patient load
author      : Sara E. Moore, Alan E. Hubbard, Rachael A. Callcut
job         : 4 February 2016
framework   : deckjs        # {io2012, html5slides, shower, dzslides, ...}
deckjs      : {theme: swiss, transition: horizontal-slide}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : zenburn       # tomorrow
widgets     : mathjax       # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
logo        : ucseal_line_k.png
#bibliography : "references.bib"
#github      :
#  user      : saraemoore
#  repo      : ColfordGroupFeb2016
--- &titleslide

<!--
prez.dir = "/path/to/parent/dir"
setwd(prez.dir)
library(slidify)
author("myprezname", open_rmd = FALSE,
	scaffold = file.path("~/Dropbox/school/berkeley/slidify_template") # only run this once
prez.dir = file.path(prez.dir, "myprezname")
setwd(prez.dir)
slidify("index.Rmd")
browseURL("index.html")
# runDeck(deckDir = prez.dir, shiny = FALSE)
publish(user = "saraemoore", repo = "ColfordGroupFeb2016")
-->

---

## Objectives

Use real-time social media data to:

* detect mass-casualty incidents and/or
* predict location-specific trauma center volume.

Goal: to build an early warning system for trauma centers.

<!-- % WHO: MCI = an event which generates more patients at one time than locally available resources can manage using routine procedures. -->

---

## Data

* Pilot analysis: Historical Twitter data on
	* Asiana Airlines crash at SFO (Jul 2013, $n \approx 400$k),
	* South Napa earthquake (Aug 2014, $n \approx 200$k),
	* Boston Marathon bombing (Apr 2013, $n \approx 1.1$ mil),
	* Marysville Pilchuck High School shooting (Oct 2014, $n \approx 250$k), and
    * Sandy Hook Elementary School shooting (Dec 2012, $n \approx 1.8$ mil).
* Currently collecting add'l data via streaming API.

<!--
Observations summarized here include only those with a `postedTime` after the event time and prior to the end of the collection period (typically 7 days after the start of the collection period). When more than 20 levels of a factor exist, only the first 20 are displayed. Counts of missing values are excluded from graphical summaries. one row corresponds to one tweet (or retweet). Boxplots are displayed for continuous variables, with a green cross denoting the mean of that variable.
-->


---

## Exploratory Analyses

[Link](file:///Users/fpgcdi/Dropbox/Trauma\ and\ Coagulation\ \(White\ Space\ Conflict\)/Twitter/prelim_analysis/index.html)

---

## Acquiring Twitter data

* [Twitter's REST APIs](https://dev.twitter.com/rest/public)
    - Conduct singular searches, read user profile information, or post Tweets.
	- Most common: [Twitter Search API](https://dev.twitter.com/rest/public/search)
    	- Limitations: [Rate limited](https://dev.twitter.com/rest/public/rate-limits) (max number of queries every 15 mins), max 100 tweets returned per query, only past 7 days, may not be complete
* [Twitter's Streaming API](https://dev.twitter.com/streaming/overview)
    - Monitor or process Tweets in real-time.
    - Most common: [Public streams](https://dev.twitter.com/streaming/public) &rarr; [POST statuses/filter](https://dev.twitter.com/streaming/reference/post/statuses/filter)
    	- Returns public statuses that match one or more filter predicates.
    	- Limitations: one connection at a time allowed per account, subject to streaming cap (max out at small percentage of total tweet volume), frequent reconnects may result in rate limiting/brief IP blocking

---

## Acquiring Twitter data, continued

* [Gnip](https://gnip.com/) (now owned by Twitter)
    - Costs $$$
    - Realtime
        - Most common: [PowerTrack](https://gnip.com/realtime/powertrack/): filtered firehose
    - Historical
        - [Historical PowerTrack](http://support.gnip.com/code/historical_powertrack.html): RESTful version of PowerTrack
        - [30-Day Search API](https://gnip.com/historical/30-day-search/)
        - [Full Archive Search API](https://gnip.com/historical/full-archive-search/)

<!--
As of April, 2015, 'firehose' access is cut off for all third-party resellers. Now it all goes through Gnip, which was acquired by Twitter in May, 2014. ([1](http://www.forbes.com/sites/benkepes/2015/04/11/how-to-kill-your-ecosystem-twitter-pulls-an-evil-move-with-its-firehose/), [2](http://thenextweb.com/dd/2015/04/11/twitter-cuts-off-firehose-resellers-as-it-brings-data-access-fully-in-house/), [3](http://www.infoworld.com/article/2908869/big-data/twitters-firehose-shut-off-is-the-newest-hazard-of-the-api-economy.html))
-->

---

## Processing Twitter data

- In R: See CRAN's ["Web Technologies" package collection](https://cran.r-project.org/web/views/WebTechnologies.html), particularly the "parsing Data from the Web" and "social media" sections
- Not in R: Twitter's list of tools for [other programming languages](https://dev.twitter.com/overview/api/twitter-libraries)

---  &twocol

## Processing Twitter data in R

*** =left

* Parsing JSON (javascript object notation) files:
	* [rjson](https://cran.r-project.org/web/packages/rjson/index.html),
	* [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html), and
	* **[jsonlite](https://cran.r-project.org/web/packages/jsonlite/index.html)**.
* Reading from REST APIs:
    * **twitteR** ([CRAN](https://cran.r-project.org/web/packages/twitteR/index.html)/[github](https://github.com/geoffjentry/twitteR))
    * [RTwitterAPI](https://github.com/joyofdata/RTwitterAPI)
* Reading from Streaming API:
    * **streamR** ([CRAN](https://cran.r-project.org/web/packages/streamR/index.html)/[github](https://github.com/pablobarbera/streamR))
    * tweet2r ([CRAN](https://cran.r-project.org/web/packages/tweet2r/index.html)/[github](https://github.com/pauarago/tweet2r))

*** =right

* Authenticating via OAuth:
    * ROAuth ([CRAN](https://cran.r-project.org/web/packages/ROAuth/index.html)/[github](https://github.com/geoffjentry/ROAuth))
* Other Resources:
   * Workshop materials and slides: [Analyzing and Collecting Social Media Data with R](https://github.com/pablobarbera/social-media-workshop)

--- &twocol

## Visualization

*** =left

[htmlwidgets](http://www.htmlwidgets.org/) (interactive JavaScript-based graphics from within R):
* [sparkline](https://github.com/htmlwidgets/sparkline) for small inline Tufte-style plots
* [DT](http://rstudio.github.io/DT/) (aka DataTables)
* [dygraphs](https://rstudio.github.io/dygraphs/) for time series
* [streamgraph](http://hrbrmstr.github.io/streamgraph/), also for time series
* [leaflet](https://rstudio.github.io/leaflet/) for mapping
* etc.

*** =right

* Use with [R Markdown](http://rmarkdown.rstudio.com) or [Shiny](http://shiny.rstudio.com/)
* **[Gallery of htmlwidgets](http://hafen.github.io/htmlwidgetsgallery/)**

---

# Thanks!
