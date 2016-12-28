# load the following packages for data manipulation and visualization
library(rgdal)
library(sp)
library(ggplot2)
library(dplyr)
library(reshape2)
library(rgeos)
library(ggthemes)
library(ggmap)
library(grid)
library(gridExtra)

# loading data and reporting names & dimensions
df <- read.csv("~/Downloads/green_tripdata_2015-09.csv")
colnames(df)
dim(df)

# Plot histogram of number of trip distance
ggplot(df, aes(Trip_distance)) + 
      geom_histogram(binwidth = 20)+
      ggtitle("Figure 1: Histogram of Trip Distance") +
      xlab("Trip Distance (Miles)") +
      ylab("Count")

## Plot of trip distance that is above 100
trip_above_100 <- df[df$Trip_distance>100,]
ggplot(trip_above_100, aes(Trip_distance)) + 
      geom_histogram(binwidth = 20) +
      ggtitle("Figure 2: Histogram of Trip Distance over 100 Miles") +
      xlab("Trip Distance (Miles)") +
      ylab("Count")

# Report mean and median trip distance by hour of day
df$hour <- format(as.POSIXct(df$Lpep_dropoff_datetime), "%H")
mean_by_hour <- df %>% group_by(hour) %>% summarise(mean(Trip_distance))
median_by_hour <- df %>% group_by(hour) %>% summarise(median(Trip_distance))
mean_median_by_hour <- mean_by_hour %>% inner_join(median_by_hour, "hour")
colnames(mean_median_by_hour) <- c("Hour", "Mean", 
                                   "Median")
mean_median_by_hour <- melt(mean_median_by_hour, "Hour")

ggplot(mean_median_by_hour, aes(Hour, value, fill = factor(variable))) + 
      geom_bar(stat = "identity", position = "stack") +
      scale_fill_discrete(name = NULL) + xlab("Hour") + 
      ylab("Trip Distance (Miles)") + 
      ggtitle("Figure 3: Mean and Median of Trip Distance by Hour")
  
# Understanding JFK trips    
## RateCodeID = 2 is for rate code at JFK region
airport_df <- df[df$RateCodeID == 2, ]

## visualizing fare amount by distance 
ggplot(airport_df, aes(Trip_distance, Fare_amount))+
      geom_point() + xlab("Trip Distance (Miles)") + 
      ylab("Fare Amount (Dollars)") + 
      ggtitle("Figure 4: Fare Amount by Trip Distance for JFK Region")

## mean of fare amount without data cleaning
mean(airport_df$Fare_amount)

## mean of fare amount with data cleaning
mean(airport_df$Fare_amount[airport_df$Fare_amount>30])

## visualizing tip by trip distance 
ggplot(airport_df, aes(Trip_distance, Tip_amount))+
      geom_point() + xlab("Trip Distance (Miles)") +
      ylab("Tip Amount (Dollars)") +
      ggtitle("Figure 5: Tip by Trip Distance for JFK Region")

## observing the number of zero distance rides
sum(airport_df$Trip_distance == 0)

## visualizing the busiest hours for JFK region
ggplot(airport_df, aes(hour))+geom_bar(stat = "count") +
      xlab("Hour") + ylab("Count") + 
      ggtitle("Figure 6: Busiest Hours for JFK Region")

# Build derived varialbe for tip as perentage of total fare
df$tipfare <- df$Tip_amount/df$Fare_amount
df$hour <- as.numeric(df$hour)
filtered_df <- df[!names(df) %in% 
                        c("lpep_pickup_datetime", 
                          "Lpep_dropoff_datetime", 
                          "Store_and_fwd_flag", 
                          "Ehail_fee")]

corr_matrix <- cor(filtered_df)

## take sample of data
filtered_df_sample <- filtered_df[1:1000, ]

## extract a sampel of the data to create a linear model 
sample_model <- lm(tipfare~., data = filtered_df_sample)
summary(sample_model)
colSums(is.na(df))
df$Ehail_fee <- NULL
tipfare_df <- df[df$tipfare<1, ]

## from the significant above, select appropriate variables
model <- lm(tipfare ~ Fare_amount+Tip_amount+ Fare_amount*Tip_amount+
                  Payment_type+Extra+MTA_tax + Extra*MTA_tax +
                  Payment_type*Extra, data = tipfare_df)
summary(model)

# Visualization of intra- vs inter-borough traffic

df <- df[, c(2,3,6,7,8,9,10,11,12)]

## rename columns for convenience
colnames(df) <- c("pdt", "ddt", "plon", "plat", "dlon", "dlat", "npnger", "trip_dis", "fare")

## data cleaning - only select passenger, trip distance, & fare > 0 
df <- df[df$npnger>0 & df$trip_dis>0 & df$fare>0,]
df <- df[, c("pdt", "ddt", "plon", "plat", "dlon", "dlat")]
df <- na.omit(df)

## Read new york borough OGR vector maps into spatial objects 
boroughs <- readOGR(path.expand("~/Downloads/nybb_16c/nybb.shp"), layer = "nybb")

## tranform borough data into following CRS
boroughs <- spTransform(boroughs, CRS("+proj=longlat +datum=WGS84"))

## create data frame for pick up longitude and latitude
pcoords <- data.frame(lon = df$plon, lat = df$plat)

## creat spatial points of coordinates data above
ppoints <- SpatialPoints(pcoords)

## format the same projection attributes for ppoint
proj4string(ppoints) <- proj4string(boroughs)

## overlay spatial points and extract BoroName (Borough Names)
df$pborough <- over(ppoints, boroughs)$BoroName

## data frame of drop off longitude and latitude
dcoords <- data.frame(lon = df$dlon, lat = df$dlat)

## create spatial points for drop off coordinates
dpoints <- SpatialPoints(dcoords)

## format the appropriate projection attributes
proj4string(dpoints) <- proj4string(boroughs)

## extract the borough names for drop off's 
df$dborough <- over(dpoints, boroughs)$BoroName

## read community district map data 
cd <- readOGR(path.expand("~/Downloads/nycd_16c/nycd.shp"), 
              layer = "nycd")

## transform the data to following CRS
cd <- spTransform(cd, CRS("+proj=longlat +datum=WGS84"))

## overlay pick up and drop off points and extract community districts
df$pcd <- over(ppoints, cd)$BoroCD
df$dcd <- over(dpoints, cd)$BoroCD

## summarize the number of rides by type and borough
pborough_summ <- count(df, pborough)
dborough_summ <- count(df, dborough)
summ_df <- data.frame(borough = pborough_summ$pborough,
                      Pickup = pborough_summ$n,
                      nd = dborough_summ$n)
summ_df <- melt(summ_df, "borough")

## visualizing the number of pick up's and drop off's by boroughs
ggplot(summ_df, aes(x = borough, y = value/1000, fill = factor(variable)))+
      geom_bar(stat = "identity", position = "dodge") +
      scale_fill_discrete(name = "Type",
                          breaks = c("np", "nd"),
                          labels = c("Pick Up's", "Drop Off's"))+
      xlab("Borough") +ylab("Total Number (in Thousands)")+
      ggtitle("Figure 7: Number of Pick-Up's and Drop-Off's by Boroughs")

## report any missing values (unclassified boroughs & community districts)
sum(is.na(df))

## remove missing values 
df <- na.omit(df)

## create a new variable that contains trip info (ie Bronx to Bronx)
df$trips <- paste(df$pborough, "to", df$dborough)

## create a simplified nyc map using gSimplify and fortify
nyc_map <- fortify(gSimplify(boroughs, 0.00001))

# fortify community district map and merge using "id" to get district id
cd_map <- merge(fortify(cd), as.data.frame(cd), by.x = "id", by.y = 0)

## Pick up regions in New York 
pp <- ggplot()+
      geom_map(data = nyc_map, map = nyc_map,
               aes(x = long, y = lat, map_id = id), 
               color = "white", fill = "black",
               size = 0.25) + 
      geom_point(data = df, aes(x = plon, y = plat), 
                 color = "blue", size = 0.1, alpha = 0.01) +
      ggtitle("Figure 8: Pick-Up Regions in New York") +
      coord_equal() + theme_map() + geom_text()+
      annotate("text", x = -73.8, y = 40.91, label = "The Bronx", 
               size = 2, color = "black") + 
      annotate("text", x = -74, y = 40.55, label = "Brooklyn",
               size = 2, color = "black") +
      annotate("text", x = -74.03, y =40.82 , label = "Manhattan",
               size = 2, color = "black") + 
      annotate("text", x = -73.75, y = 40.81, label = "Queens", 
               size = 2, color = "black") +
      annotate("text", x = -74.2, y = 40.67, label = "Staten Island",
               size = 2, color = "black")


dp <- ggplot()+
      geom_map(data = nyc_map, map = nyc_map,
               aes(x = long, y = lat, map_id = id), 
               color = "white", fill = "black",
               size = 0.25) + 
      geom_point(data = df, aes(x = dlon, y = dlat), 
                 color = "green", size = 0.1, alpha = 0.01) +
      ggtitle("Figure 9: Drop-Off Regions in New York") +
      coord_equal() + theme_map() + geom_text()+
      annotate("text", x = -73.8, y = 40.91, label = "The Bronx", 
               size = 2, color = "black") + 
      annotate("text", x = -74, y = 40.55, label = "Brooklyn",
               size = 2, color = "black") +
      annotate("text", x = -74.03, y =40.82 , label = "Manhattan",
               size = 2, color = "black") + 
      annotate("text", x = -73.75, y = 40.81, label = "Queens", 
               size = 2, color = "black") +
      annotate("text", x = -74.2, y = 40.67, label = "Staten Island",
               size = 2, color = "black")

grid.arrange(pp, dp, ncol = 2)

## count the number of pick up's and drop off's
pcd <- count(df, pcd)
dcd <- count(df, dcd)

## rename columns 
colnames(pcd) <- c("BoroCD", "Pick Up")
colnames(dcd) <- c("BoroCD", "Drop Off")

## merge pcd and dcd data together 
cd_map <- cd_map %>% inner_join(pcd, "BoroCD") %>% inner_join(dcd, "BoroCD")
cd_map$`Pick Up` <- cd_map$`Pick Up`/1000
cd_map$`Drop Off` <- cd_map$`Drop Off`/1000
cd_map <- melt(cd_map, colnames(cd_map)[1:10])

## plot pick up and drop off level by community district
ggplot(cd_map, aes(long, lat, group = group, fill = value)) +
      geom_polygon() +
      coord_equal() +
      theme_map() +
      facet_wrap(~variable) +
      ggtitle("Figure 10: Pick Up and Drop Off Level by District") +
      theme(legend.position = "bottom")

## Intraborough traffic visualization
brooklyn <- df[df$trips == "Brooklyn to Brooklyn",]
manhattan <- df[df$trips == "Manhattan to Manhattan",]
queens <- df[df$trips == "Queens to Queens",]
bronx <- df[df$trips == "Bronx to Bronx",]
staten <- df[df$trips == "Staten Island to Staten Island",]

## Introborough plot function
intraboro_plot <- function(boro) {
      p <- ggplot()+
            ## plot new york city map
            geom_map(data = nyc_map, map = nyc_map,
                     aes(x = long, y = lat, map_id = id), 
                     color = "white", fill = "darkgray",
                     size = 0.25) + 
            theme_map() + coord_equal() + 
            ## plot segments
            geom_segment(data = boro, aes(x = plon, y = plat, 
                                        xend = dlon, yend = dlat), 
                         color = "navyblue",
                         alpha = 0.1,
                         size = 0.01) 
      return(p)
}

## brooklyn
intraboro_plot(brooklyn) + 
      ggtitle("Figure 11: Intraborough Traffic Visualization of Brooklyn")
## manhattan
intraboro_plot(manhattan) + 
      ggtitle("Figure 12: Intraborough Traffic Visualization of Manhattan")
## queens
intraboro_plot(queens) + 
      ggtitle("Figure 13: Intraborough Traffic Visualization of Queens")
## bronx
intraboro_plot(bronx) + 
      ggtitle("Figure 14: Intraborough Traffic Visualization of Bronx")
## staten
intraboro_plot(staten) + 
      ggtitle("Figure 15: Intraborough Traffic Visualization of Staten Island")

## count the number of trips by trip types
trips_count <- count(df, trips)
trips_count <- trips_count[order(trips_count$n, decreasing = T),]
trips_count$n <- trips_count$n/1000

## visualization of trips by trip types
ggplot(trips_count, aes(reorder(trips, n), n)) +
      geom_bar(stat = "identity") +
      coord_flip()+ ylab("Number of Trips (in Thousands)") +
      xlab("Trip Types") + 
      ggtitle("Fig 16: Number of Trips by Trip Types")

## Interborough Visualization

pBrooklyn <- df[df$pborough == "Brooklyn",]
pManhattan <- df[df$pborough == "Manhattan",]
pBronx <- df[df$pborough == "Bronx",]
pStatenIsland <- df[df$pborough == "Staten Island",]
pQueens <- df[df$pborough == "Queens",]

## function that plot intraborough given the borough name
interboro_vis <- function(pborough) {
      p <- ggplot() +
            geom_map(data = nyc_map, map = nyc_map,
                     aes(x = long, y = lat, map_id = id), 
                     color = "white", fill = "black",
                     size = 0.25)+
            geom_segment(data = pborough, aes(x = plon, y = plat, 
                                            xend = dlon, yend = dlat), 
                         col = "gold", size = 0.03) + 
            facet_wrap(~dborough)+
            coord_equal() + theme_map() 
      return(p)
}

## Interborough traffic visualization by Borough
interboro_vis(pBronx) +
      ggtitle("Figure 17:Interborough Traffic: Pick Up at The Bronx")
interboro_vis(pBrooklyn)+
      ggtitle("Figure 18: Interborough Traffic: Pick Up at Brooklyn")
interboro_vis(pManhattan)+
      ggtitle("Figure 19: Interborough Traffic: Pick Up at Manhattan")
interboro_vis(pQueens)+
      ggtitle("Figure 20: Interborough Traffic: Pick Up at Queens")
interboro_vis(pStatenIsland)+
      ggtitle("Figure 21: Interborough Traffic: Pick Up at Staten Island")
