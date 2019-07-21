---
layout: post
title: Downloading Historical Security Prices
date:   2019-07-21 21:31:09 -0400
feature_image: "/assets/bridge.jpg"
categories: jekyll update
---

I've spent years honing my economic acumen and valuation modeling techniques, but what good are
those skills if I don't have an easy and efficient way to access the large volume of data needed to
run meaningful analysis? I'm going to teach you a method to overcome one of he biggest hurdles to 
getting started: Actually getting the data to conduct research. I developed a system to automate this 
process by creating a function that utilizing the ```tseries``` package in R to cache historical security 
prices onto my local drive, which effectively "stages" the data in order to run the actual analysis.

Much of the functionality here was developed by using [Financial Analytics with R by Bennett & Hugen](https://www.amazon.com/Financial-Analytics-Building-Laboratory-Science/dp/1107150752/ref=sr_1_3?crid=MV3JU6ASDB9W&keywords=financial+analytics+with+r&qid=1563721706&s=gateway&sprefix=financial+analytics%2Caps%2C123&sr=8-3) as a guide.  

### The Following code will create a sub-directory in your source file location on your local drive  
All you need to do is feed the function a vector of ticker symbols (datatype must be as.character). You can specify the 
start/end dates, but it defaults to the trailing 12 months. Downloading the 5 year historical returns of all 
505 securities in the S&P 500 took sub 3.5 minutes on my machine.

```
library(tseries)
create_price_dir <- function(labels, start_date=Sys.Date()-365, end_date=Sys.Date()-1) {
  path <- dirname(rstudioapi::getSourceEditorContext()$path)
  setwd(path)
  #days <- length(get.hist.quote('AAPL', start = start_date, end = end_date, quote = 'Adj'))
  len <- length(labels)
  try(dir.create("data"))
  k <- 1
  for (i in labels) {
    try({
      prices <- as.vector(get.hist.quote(i, start = start_date, end = end_date, quote = 'Adj'))
      #print(prices)
      dates <- time(get.hist.quote(i, start = start_date, end = end_date, quote = 'Adj'))
      bound <- cbind(as.character(dates), prices)
      file_name <- paste(path, "/data/", "cached", i, ".csv", sep = '')
      write.csv(bound, file = file_name, row.names = F)
      print(paste(k, "of", len, "done", sep = " "))
      k <- k+1
    }, silent = T)
  }
  return(paste("Done with", len-k, "errors"))
}

```
...and there you have it!
[](\assets\cache_folder.PNG)
