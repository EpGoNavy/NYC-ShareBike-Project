# NYC-ShareBike-Project

# Project overview
Bike Sharing in the US, is rising and this project is to understand who are
the riders. For this analysis, I used datasets for years 2016 - 2018 which can
be found here https://s3.amazonaws.com/tripdata/index.html. More information
 about the data  sets can be found here https://www.citibikenyc.com/system-data.   

# Prepping and cleaning the datasets
I used R for most of my data cleaning to create a file that would be used to
prep my file for ML. After combine all three years there were approximately
~800k lines

Here is the code I used to create new columns and populate:
nycbikedata <-read.csv("C://Users/ep927/Documents/NewYorkCity/CleanedBikeData.
csv", header = TRUE)

nycbikedata$Start_Year <- strptime(nycbikedata$Start_Time, "%m/%d/%Y %H:%M")

nycbikedata$Year <- year(nycbikedata$Start_Year)

nycbikedata$Month <- month(nycbikedata$Start_Year)

nycbikedata$DayOfWeek <- wday(nycbikedata$Start_Year, label = TRUE, abbr = TRUE)

nycbikedata$Qtr <- quarter(nycbikedata$Start_Year)

nycbikedata$DayOfWeek <- ifelse(wday(nycbikedata$Start_Year)==1,7,
wday(nycbikedata$Start_Year)-1)

nycbikedata$Age <- (nycbikedata$Year - nycbikedata$Birth_Year)

nycbikedata$distance <-distHaversine(nycbikedata[,6:7],
nycbikedata[,10:11],r=6378137) / 1609.344

nycbikedata$RideInMin <- round(nycbikedata$Trip_Duration / 60 ,digits = 0)

str(nycbikedata)
summary (nycbikedata)  data derived from the original file.


# EDA (exploratory data analysis)
The tool I used for my data exploration was done in R. These are a few examples
used for my data exploration.

![RidesByMinute](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/Rides%20Per%20Minute.png)

![CountOfRidersByAge](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/Count%20of%20Riders%20by%20Age.png)

![MeanDistanceRiden](https://raw.githubusercontent.com/EpGoNavy/NYC-ShareBike-Project/master/Images/MeanDistanceByAge.png)

# Analysis
Selecting the fields to be used in predicting Gender using RandomForset.  Since
Gender is a int, and I need to convert it to a Factor.  To do that Here is the
code I used. Because the dataset was unbalanced I needed to use a command called
stratified, to help balance the factor(gender).  In this case I used 100K.
These are the fields I'm selecting for the ML.  
df <- select (nycbikedata, Age, Gender, Year, Month,DayOfWeek,distance,RideInMin,
Start_Station_ID, End_Station_ID)

small.sample <- stratified(df, "Gender", size=10000)

small.sample$Gender <- as.factor(small.sample$Gender)

![GenderBalancing](https://raw.githubusercontent.com/EpGoNavy/NYC-ShareBike-Project/master/Images/GenderBalancing.PNG)


# ML RandomForset - MODELING AFTER TUNING
* Step 1.
  Tune my try

  train <- as.data.frame(train)

  t <- tuneRF(train[,-2], train[,2],
       stepFactor = 0.5,
       plot=TRUE,
       improve = 0.05)



* Step 2. Tune the model using MyTry from the above
  Tune my model using the information from mtry I used that information to
  tune my model.

       rf <-randomForest(Gender~., data = train,
                         ntree = 400,
                         mtry = 4,
                         importance = TRUE,
                          proximity = TRUE)

       rf$confusion
       hist(treesize(rf),
            main = "Number Of Trees",
            col = "green")

![PostModelTuneCM](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/PostModelTune.PNG)

![NumberOfTrees](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/NumberOfTrees.PNG)

* Step 3. Variable importance

#Importance of Variables

varImpPlot(rf,
           sort = TRUE,
           n.var = 6,
           main = "Top Six - Variable Importance")

varUsed(rf)  

![Variable Used](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/Variable_Importance.PNG)

# Conclusions


# References
