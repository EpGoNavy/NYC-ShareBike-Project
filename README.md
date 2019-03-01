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

# ML models RandomForset - PRIOR TO TUNING THE MODEL
The model I chose for this analysis is RandomForset.  

* Step 1
  Data Partition
  ind = Independent Variable. The data will be split 70/30
  rf = Random Forest

  set.seed(123)
  ind <- sample(2, nrow(small.sample), replace=TRUE, prob = c(0.7, 0.3))
  train <- small.sample[ind==1,]
  test <- small.sample[ind==2,]

* Step 2
  Random Forset - Prior to Tuning the model

  set.seed(111)
  rf <-randomForest(Gender~., data = train)
  print(rf)
  attributes(rf)

![Model_Prior_To_Tuning](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/TrainDataSetPriorToTuning.PNG)

* Step 3.
  Prediction and Confusion Matrix - Train Data
  Pred1 = Predication 1

  pred1 <- predict(rf, train)
  confusionMatrix(pred1,train$Gender)

![CM_Pre_Train_Data](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/ConfusionMatrixTrainDataPriorToTuning.PNG)

* Step 4.
  Prediction and Confusion with Matrix - Test Data
  Pred2 = Prediction 2

  pred2 <- predict(rf, test)  
  confusionMatrix(pred2, test$Gender)

![CM_Pre_Test_Data](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/ConfusionMatrixTestDataPriorToTuning.PNG)

* Step 5.
  Printing the Error Rate to determine the number of trees to use.  
  plot(rf)
  m
![Error_RF](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/ErrorRate-RF.PNG)

# ML RandomForset - MODELING AFTER TUNING
* Step 1.
  Tune my try

  train <- as.data.frame(train)
  t <- tuneRF(train[,-2], train[,2],
       stepFactor = 0.5,
       plot=TRUE,
       improve = 0.05)

       ![Tune My Try](https://github.com/EpGoNavy/NYC-ShareBike-Project/blob/master/Images/MyTryTune.PNG)

* Step 2. Tune the model using MyTry from the above step
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



* Step 3. After modeled tuned - Train Data
  pred1 <- predict(rf, train)
  confusionMatrix(pred1,train$Gender)

* Step 4. After Modeled tuned - Test Set

  pred2 <- predict(rf, test)
  confusionMatrix(pred2, test$Gender)




# Conclusions


# References
