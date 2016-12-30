# Exploratory Analysis, Predictive Modeling, and Visualization of New York Traffic Data
by Davin Kaing

## Background & Introduction
Analyzing millions of traffic can be a daunting task due to the large volume and potential hidden insights. In this project, I analyzed New York traffic data (September 2015) by performing the following: exploratory data analysis, predictive modeling, and visualization.

## Exploratory Data Analysis
After the data is loaded into R, the dimension of the dataset is explored. A histogram of Trip Distance is plotted to examine the distribution. As shown in Figure 1, the histogram shows that the distribute of trip distance is highly right-skewed. This means that there are some potential outliers hidden in the dataset. To further visualize these outliers, a histogram plot of trip distance above 100 miles was plotted (Figure 2). This indeed shows that there are a few trip distances that are a few trips above 100 miles and these could possibly be outliers.

![](http://i.imgur.com/XkY99gz.png)
![](http://i.imgur.com/ehPZiOm.png)

Other attributes of the data were also explored. The mean and median by hours (Figure 3) were also explored and the analysis shows that at 5 and 6 am, the trip distances appear to be the longest. Activities in the JFK region were also examined. A scatter plot of trip distance and fare amount (Figure 4) shows that some fare amounts are zero and some are negative values. These are possible outliers in the data. The mean of the distances with and without the outliers were computed. With the outliers, the mean of fare amount is $49.02 and without, it is $52. It is likely that the true value is $52 as the fare amounts without the outlers are all $52. Another interesting finding is that the fare amount for JFK region is fixed irrespective of the trip distance. Another interesting finding is that for the tip by trip distance plot (Figure 5), there are a few high tips where the distance is zero. Again, these values could be the outliers. There's also no correlation between trip distance and tip. It appeared to average a certain value for all trip distances. Lastly, it was interesting to observe that the busiest times for JFK region (Figure 6) is 18 (6 pm) and 17 (5 pm).

![](http://i.imgur.com/ISqhFdg.png)
![](http://i.imgur.com/Xo6z6LE.png)
![](http://i.imgur.com/AH9Cakn.png)
![](http://i.imgur.com/9uD1pFa.png)

## Predictive Modeling
To predict the tip to fare ratio. I first built a derived variable titled "tipfare." Afterwards, I filtered out factor variables. A correlation matrix was also performed to examine possible correlation to tipfare. For efficiency, a small sample of the data was extracted and modeled, the most associated variables can then be extracted from this model. With the highly correlated variables, the linear model was built with interaction terms. The R squared for this model is 0.7722.

## Visualization
### Preprocessing
To easily code the visualization part, the appropriate variables were extracted and renamed. Afterwards, the type of boroughs the trip coordinates fall in is also determined using the new york borough map data. The map data was read in and tranformed. Afterwards, the coordinated of the data were map to the borough data and the borough region was then determined. The community district data is also imported and transformed in the same manner as above. The districts in the data were then determined. The number of rides by type and borough can be summarized. Figure 7 shows that the highest number of pick ups reside in Brooklyn and the highest number of drop offs is in Manhanttan.

![](http://i.imgur.com/SHZZtgh.png)

After the preprocessing and exploratory visualization, the missing values are filtered out and a new variable "trip" is created to summarize the trip destinations.

Figure 8 and Figure 9 show the pick-up and drop-off regions in New York. As shown, the number of pick up's are more concentrated in certain regions of the boroughs and the drop-off's are more scattered. Also, there is very little trip activiy in Staten Island.

![](http://i.imgur.com/EGuKJoL.png)
![](http://i.imgur.com/8AwJKg8.png)

The number of pick and drop off region by community districts is also explored. Here, it shows that the number of pick up's is greater and more concentrated than the number of drop offs.

![](http://i.imgur.com/xEfis6D.png)

The follwoing plots (Figure 11 - Figure 15) visualize the intraborough traffic. In Brooklyn (Figure 11), the highest level of activity is in the north. In Manhattan (Figure 12), the level of activity is consistent throughout the borough. In Queens (Figure 13), there is highest activity in the west. As of The Bronx (Figure 14), the highest level of activity is in the south. Finally, in Staten Island, there's very little to zero activity.

![](http://i.imgur.com/TJIMANS.png)

![](http://i.imgur.com/rV4LqX7.png)

![](http://i.imgur.com/bUQUN3t.png)

![](http://i.imgur.com/VPhZQ91.png)

![](http://i.imgur.com/bYC61Tw.png)

The number of trips by trip types was also visualized (Fig 16). Here, it is clear that the highest number of activity is in Brooklyn, following by Manhattan and Queens. For the interborough trips, the highest is in Brooklyn to Manhattan, Manhattan to Bronx, and Queens to Manhattan.

![](http://i.imgur.com/cPXr7JP.png)

For the interborough visualizations, I subset the data by pick up locations. Then the destinations were visualized. For interborough traffice with The Bronx (Figure 17) as the original location, there is a high trip activity with Manhattan and Queens. As of Brooklyn (Figure 18), there's a high activity with Manhattan and Queens. For Manhattan (Figure 19), there's activity throughout Brooklyn Bronx and Queens. As of Queens (Figure 20), the highest activities appear to be with Brooklyn and Manhattan. Finally, for Staten Island (Figure 21), there is little to no activity.

![](http://i.imgur.com/laFNK0n.png)

![](http://i.imgur.com/03xchqT.png)

![](http://i.imgur.com/soXIRSt.png)

![](http://i.imgur.com/ZUVA0eC.png)

![](http://i.imgur.com/Pq5HKdb.png)

## Conclusion
From the analysis above, it is evident that certain regions of New York higher activity of traffic that others. Also, the interborough traffice varies for the different boroughs.

## References
City of New York. (2015, September 22). Retrieved from http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml
City of New York. (n.d.). Retrieved form http://www1.nyc.gov/site/planning/data-maps/open-data/districts-download-metadata.page
Roger Bivand,Tim Keitt, Barry Rowlingson, Edzer Pebesma, Michael Sumner, Robert Hijmans, and Even Rouault. (2016, May 11). Package 'rgdal'. Retrieved from https://cran.r-project.org/web/packages/rgdal/rgdal.pdf
David Kahle and Hadley Wickham. (2013, June). ggmap: Spatial Visualization with ggplo2. Retrieved from https://journal.r-project.org/archive/2013-1/kahle-wickham.pdf
Edzer Pebesma, Roger Bivand, Barry Rowlingson, Virgilio Gomez-Rubio, Robert Hijmans, Michael Sumner, Don MacQueen, Jim Lemon, and Josh O'Brien. (2016, April 14). Package 'sp'. Retrieved from ftp://cran.r-project.org/pub/R/web/packages/sp/sp.pdf








