---
layout: post
title: K-Medoid Cluster Analysis on Equity Securities
date:   2019-07-07 21:31:09 -0400
feature_image: "/assets/bridge.jpg"
categories: jekyll update
---

If you've had the opportunity to study finance, even in the most basic college courses you learn that the 
value of an asset is derived in part by the opportunity costs of readily available alternatives. For instance, 
the value of a high dividend paying equity security decreases when interest rates increase. There exists this 
inverse relationship because a rational investor won't have the willingness to hold the risk of an equity security
if a less-risky alternative (like fixed income) is able to provide a reasonable return. These cashflow producing
assets, in the marketplace, are "competing" for the same investor capital. If there is an interest rates increase, 
on a relaive basis, dividend paying equities become less attractive and the price subsequently decreases.  

The reason this is important is because one of the primary methods of estimating the instrinsic or "fair value"
of a security is by comparing it to the value of its peers. One of the challenges when conducting this analysis
is finding pure-play comparables by which to compare and contrast. Let's say, for example, that you wanted to 
find the most pure-play comparable to Amazon. What company would you choose? Walmart? Alibaba? Netflix? There are good reasons
to choose these as comps. There are legitimate reasons to do so, but none of these 
are really a good option because Amazon doesn't compete in just ONE industry. It competes in all of them.  The correct answer would be some
combination of companies thereof.  

My proposed solution to this problem is to do some data-mining and allow the data to speak for itself. 
![](\assets\clusplot.jpeg)
Let's suppose that we have historical price data on all the S&P 500 constituents in the following format:  

Date | prices
--- | ---
2014-07-22 | 86.81319
2014-07-23 | 89.07702
2014-07-24 | 88.93037
2014-07-25 | 89.51695
2014-07-28 | 90.75424
2014-07-29 | 90.16768

The following user-defined function takes this data and translates the historical price information into log returns and 
outputs it into a format where we can run a k-medoid clustering algorithm to determine what the appropriate 
comparables should be. 

```
calc_returns2 <- function(df, days_per_period=125) {
  date_lims <- range(as.Date(df$X))
  yrs <- as.numeric(range(as.Date(df$X))[2] - range(as.Date(df$X))[1])/365.25
  trde_days <- ceiling(252*yrs)
  segs <- ceiling(trde_days/days_per_period)
  date_cuts <- seq.Date(from = date_lims[1], to = date_lims[2], length.out = segs+1)
  ret_list <- c()
  for (i in 1:length(date_cuts)-1) {
    ret_list[i] <- mean(diff(log(df$prices[as.Date(df$X) > as.Date(date_cuts[i]) & as.Date(df$X) < as.Date(date_cuts[i+1])])))
  }
  return(ret_list)
}
```
Let's assume that you have one dataframe for every security we are analyzing. For more information on accessing this kind of 
data, please [check out this post](https://jbquant.github.io/jekyll/update/2019/07/22/caching-stock-prices.html). In my demonstration, 
I have the trailing 5 years of price information and I want to look at the 6 month returns over the last 5 years, so 10 return periods.
I create a dataframe with the dimensions 502x10, or one row for every security and one column for every return period. Then I loop
through all the files in the directory and uses the calc_returns2 function so you have your final dataframe. 
```
files_list <- c(list.files(<insert path to directory with files>)

final.df <- data.frame(row.names = tickers, 
                       "period_1"=rep(NA, length(tickers)), 
                       "period_2"=rep(NA, length(tickers)), 
                       "period_3"=rep(NA, length(tickers)), 
                       "period_4"=rep(NA, length(tickers)), 
                       "period_5"=rep(NA, length(tickers)), 
                       "period_6"=rep(NA, length(tickers)), 
                       "period_7"=rep(NA, length(tickers)), 
                       "period_8"=rep(NA, length(tickers)), 
                       "period_10"=rep(NA, length(tickers)))

for (i in 1:length(files_list)) {
  elem <- read.csv(<insert path to files>, header = T)
  final.df[i,] <- calc_returns2(elem, days_per_period = 125)
}
write.csv(x = final.df, file = "sp_annual_returns.csv")  
```
With this dataframe, we can run the k-medoid algorithm. 
```
k <- 12
final.df.clean <- na.omit(final.df)
my_clusters <- pam(x = final.df.clean, k)
my_clusters$clustering

plot(final.df.clean[,3:4],col=rainbow_hcl(max(my_clusters$clustering))[my_clusters$clustering],main="k-Medoids Clusters")
points(my_clusters$medoids,pch=19,cex=1.5,col="navyblue")
```
If we isolate just two of the periods, we can get a closer look at how the various securities were grouped.
Each of these points represents one of the securities and each of the navy blue dots represents the 
k-medoid center.
![](\assets\k_medoid.jpeg)  
And here is a more comprehensive version that graphs based on the Principle Components:  
```
fviz_cluster(object = my_clusters[1:10], data = final.df.clean[1:10])
```
![](\assets\clusters.jpeg)
