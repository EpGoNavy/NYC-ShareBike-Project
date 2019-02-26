# NYC-ShareBike-Project

# Project overview
Bike Sharing in the US, is rising and this project is to understand who are the riders. For this analysis, I used datasets for years 2016 - 2018 which can be found here https://s3.amazonaws.com/tripdata/index.html. More information about the data sets can be found here https://www.citibikenyc.com/system-data.   

# Prepping and cleaning the datasets
I used R for most of my data cleaning to create a file that would be used to prep my file for ML.  
Here is the code I used to create new columns and populate nycbikedata <- read.csv("C://Users/ep927/Documents/NewYorkCity/CleanedBikeData.csv", header = TRUE)


nycbikedata$Start_Year <- strptime(nycbikedata$Start_Time, "%m/%d/%Y %H:%M")

nycbikedata$Year <- year(nycbikedata$Start_Year)
nycbikedata$Month <- month(nycbikedata$Start_Year)
nycbikedata$DayOfWeek <- wday(nycbikedata$Start_Year, label = TRUE, abbr = TRUE)
nycbikedata$Qtr <- quarter(nycbikedata$Start_Year)
nycbikedata$DayOfWeek <- ifelse(wday(nycbikedata$Start_Year)==1,7,wday(nycbikedata$Start_Year)-1)
nycbikedata$Age <- (nycbikedata$Year - nycbikedata$Birth_Year)
nycbikedata$distance <-distHaversine(nycbikedata[,6:7], nycbikedata[,10:11],r=6378137) / 1609.344
nycbikedata$RideInMin <- round(nycbikedata$Trip_Duration / 60 ,digits = 0)


str(nycbikedata)
summary(nycbikedata) data derived from the original file.


# EDA (exploratory data analysis)
The tool I used for my data exploration was done in R.
![C:\Users\ep927\Documents\NewYorkCity]()



# Analysis

* ML models

# Conclusions


# References
