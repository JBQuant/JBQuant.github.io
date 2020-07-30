---
layout: post
title: Save Time through Simple Automation
date:   2020-07-29 21:31:09 -0400
feature_image: "/assets/bridge.jpg"
categories: jekyll update
---

Automation - the biggest disrupter of the decade... 

Let me start by saying this: computers are dumb - they are, however, very good at following instructions - very, very explicit instructions, actually. 

They also, 1.) don’t get tired, 2.) need bathroom breaks, 3.) need a living wage, or 4.) complain (well, sometimes maybe)

If you find yourself doing the same redundant task, or running the same report every morning, there’s a good change you could save yourself that time with a few simple steps using R code and R functionality. 

For instance, if you’re familiar with, and regularly use Rmarkdown files, check out the render() function in the rmarkdown library.

```
rmarkdown::render("C:/Users/<Your Directory>/<Your Rmarkdown File>.Rmd",   
                  output_file = <output file name>, 
                  output_dir = <output file directory>)
```

Add this code to a separate script, and have that script [run via the Rscript command on the Task Scheduler on your PC](https://www.r-bloggers.com/automating-and-scheduling-r-scripts-in-windows-tutorial/), at a certain time in the morning (I have mine run at 5AM, or when I log on in the morning), and BOOM, whether the report is a pdf, html, or a slide deck you’ll be ready to go first thing. 
I have my report run risk analysis on a portfolio of equity and fixed income securities, so I know exactly what what has changed in my portfolio in regards to performance, or expected risk. 