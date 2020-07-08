# Data-Science-Project-Uber-Data-Analysis-R-

In today’s R project, we will analyze the Uber Pickups in New York City dataset. This is more of a data visualization project that will guide you 
towards using the ggplot2 library for understanding the data and for developing an intuition for understanding the customers who avail the trips. 

library(ggplot2)
library(ggthemes)
library(lubridate)
library(dplyr)
library(tidyr)
library(DT)
library(scales)

setwd('C:/Users/Pranto/Desktop/ubar data analysis')

colors = c(""#CC1011", "#665555", "#05a399", "#cfcaca", "#f5e840", "#0683c9", "#e075b0"")
           

uber.raw.data.apr14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-apr14.csv/uber-raw-data-apr14.csv")  
uber.raw.data.may14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-may14.csv/uber-raw-data-may14.csv")
uber.raw.data.jun14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-jun14.csv/uber-raw-data-jun14.csv")
uber.raw.data.jul14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-jul14.csv/uber-raw-data-jul14.csv")
uber.raw.data.aug14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-aug14.csv/uber-raw-data-aug14.csv")
uber.raw.data.sep14 <- read.csv("C:/Users/Pranto/Desktop/dataset/Uber-dataset/Uber-dataset/uber-raw-data-sep14.csv/uber-raw-data-sep14.csv")


data_2014 <- rbind(uber.raw.data.apr14, uber.raw.data.may14, uber.raw.data.jun14, uber.raw.data.jul14, uber.raw.data.aug14, uber.raw.data.sep14 )

data_2014$Date.Time <- as.POSIXct(data_2014$Date.Time, format = "%m/%d/%Y %H:%M:%S")


data_2014$Time <- format(as.POSIXct(data_2014$Date.Time, format = "%m/%d/%Y %H:%M:%S"), format="%H:%M:%S")

data_2014$Date.Time <- ymd_hms(data_2014$Date.Time)

data_2014$day <- factor(day(data_2014$Date.Time))

data_2014$month <- factor(month(data_2014$Date.Time, label = TRUE))

data_2014$year <- factor(year(data_2014$Date.Time))

data_2014$dayofweek <- factor(wday(data_2014$Date.Time, label = TRUE))


data_2014$hour <- factor(hour(hms(data_2014$Time)))

data_2014$minute <- factor(minute(hms(data_2014$Time)))

data_2014$second <- factor(second(hms(data_2014$Time)))


#Plotting the trips by the hours in a day

hour_data <- data_2014 %>%
  group_by(hour) %>%
  dplyr::summarize(Total = n()) 
datatable(hour_data)



ggplot(hour_data, aes(hour, Total)) + 
  geom_bar( stat = "identity", fill = "steelblue", color = "red") +
  ggtitle("Trips Every Hour") +
  theme(legend.position = "none") +
  scale_y_continuous(labels = comma)



month_hour <- data_2014 %>%
  group_by(month, hour) %>%
  dplyr::summarize(Total = n())


ggplot(month_hour, aes(hour, Total, fill = month)) + 
  geom_bar( stat = "identity") +
  ggtitle("Trips by Hour and Month") +
  scale_y_continuous(labels = comma)



#Plotting data by trips during every day of the month


day_group <- data_2014 %>%
  group_by(day) %>%
  dplyr::summarize(Total = n()) 
datatable(day_group)

ggplot(day_group, aes(day, Total)) + 
  geom_bar( stat = "identity", fill = "steelblue") +
  ggtitle("Trips Every Day") +
  theme(legend.position = "none") +
  scale_y_continuous(labels = comma)



day_month_group <- data_2014 %>%
  group_by(month, day) %>%
  dplyr::summarize(Total = n())


ggplot(day_month_group, aes(day, Total, fill = month)) + 
  geom_bar( stat = "identity" ) +
  ggtitle("Trips by Day and Month") +
  scale_y_continuous(labels = comma) +
  scale_fill_manual(values = colors)

#Number of Trips taking place during months in a year
month_group <- data_2014 %>%
  group_by(month) %>%
  dplyr::summarize(Total = n()) 
datatable(month_group)


ggplot( month_group, aes(month, Total, fill = month)) + 
  geom_bar( stat = "identity") +
  ggtitle("Trips by Month") +
  theme(legend.position = "none") +
  scale_y_continuous(labels = comma) +
  scale_fill_manual(values = colors)


month_weekday <- data_2014 %>%
  group_by(month, dayofweek) %>%
  dplyr::summarize(Total = n())


ggplot(month_weekday, aes(month, Total, fill = dayofweek)) + 
  geom_bar( stat = "identity", position = "dodge") +
  ggtitle("Trips by Day and Month") +
  scale_y_continuous(labels = comma) +
  scale_fill_manual(values = colors)


#Finding out the number of Trips by bases


ggplot(data_2014, aes(Base)) + 
  geom_bar(fill = "darkred") +
  scale_y_continuous(labels = comma) +
  ggtitle("Trips by Bases")

ggplot(data_2014, aes(Base, fill = month)) + 
  geom_bar(position = "dodge") +
  scale_y_continuous(labels = comma) +
  ggtitle("Trips by Bases and Month") +
  scale_fill_manual(values = colors)


ggplot(data_2014, aes(Base, fill = dayofweek)) + 
  geom_bar(position = "dodge") +
  scale_y_continuous(labels = comma) +
  ggtitle("Trips by Bases and DayofWeek") +
  scale_fill_manual(values = colors)


#Creating a Heatmap visualization of day, hour and month

day_and_hour <- data_2014 %>%
  group_by(day, hour) %>%
  dplyr::summarize(Total = n())
datatable(day_and_hour)

ggplot(day_and_hour, aes(day, hour, fill = Total)) +
  geom_tile(color = "white") +
  ggtitle("Heat Map by Hour and Day")

ggplot(day_month_group, aes(day, month, fill = Total)) +
  geom_tile(color = "white") +
  ggtitle("Heat Map by Month and Day")

ggplot(month_weekday, aes(dayofweek, month, fill = Total)) +
  geom_tile(color = "white") +
  ggtitle("Heat Map by Month and Day of Week")


month_base <-  data_2014 %>%
  group_by(Base, month) %>%
  dplyr::summarize(Total = n()) 


day0fweek_bases <-  data_2014 %>%
  group_by(Base, dayofweek) %>%
  dplyr::summarize(Total = n()) 


ggplot(month_base, aes(Base, month, fill = Total)) +
  geom_tile(color = "white") +
  ggtitle("Heat Map by Month and Bases")


ggplot(day0fweek_bases, aes(Base, dayofweek, fill = Total)) +
  geom_tile(color = "white") +
  ggtitle("Heat Map by Bases and Day of Week")


#Creating a map visualization of rides in New York

min_lat <- 40.5774
max_lat <- 40.9176
min_long <- -74.15
max_long <- -73.7004


ggplot(data_2014, aes(x=Lon, y=Lat)) +
  geom_point(size=1, color = "blue") +
  scale_x_continuous(limits=c(min_long, max_long)) +
  scale_y_continuous(limits=c(min_lat, max_lat)) +
  theme_map() +
  ggtitle("NewYork_City MAP BASED ON UBER RIDES DURING 2014 (APR-SEP)")

ggplot(data_2014, aes(x=Lon, y=Lat, color = Base)) +
  geom_point(size=1) +
  scale_x_continuous(limits=c(min_long, max_long)) +
  scale_y_continuous(limits=c(min_lat, max_lat)) +
  theme_map() +
  ggtitle("NewYork_City MAP BASED ON UBER RIDES DURING 2014 (APR-SEP) by BASE")


