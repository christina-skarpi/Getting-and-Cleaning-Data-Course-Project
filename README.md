# Getting-and-Cleaning-Data-Course-Project

#!/usr/bin/env Rscript

# Downloaded and unzipped the dataset needed for the course. 
# The datasets is placed in the same directory with the run_analysis.R script, 
# under the dataset directory. 

# Load the nesessairy libraries
library(data.table)
library(dplyr)

# get the current date of the dataset downloaded just for future reference if needed. 
dateDownload <- date()

#Start reading files
setwd("C:/Users/skarpath/Documents/Rproject")

# Read the features files test and train to a dataframe
featuresTest <- read.table("./dataset/test/X_test.txt", header = F)
featuresTrain <- read.table("./dataset/train/X_train.txt", header = F)

# Read the features names test and train to a dataframe
featuresNames <- read.table("./dataset/features.txt", header = F)

# Read the activity files test and train to a dataframe
activityTest <- read.table("./dataset/test/y_test.txt", header = F)
activityTrain <- read.table("./dataset/train/y_train.txt", header = F)

# Read the activity labels test and train to a dataframe
activityLabels <- read.table("./dataset/activity_labels.txt", header = F)

# Read the subject files test and train to a dataframe
subjectTest <- read.table("./dataset/test/subject_test.txt", header = F)
subjectTrain <- read.table("./dataset/train/subject_train.txt", header = F)

### Step1 Merges the training and the test sets to create one data set.
# Merge the test & train dataframes for the features 
featuresData <- rbind(featuresTest, featuresTrain)

# Merge the test & train dataframes for the activity 
activityData <- rbind(activityTest, activityTrain)

# Merge the test & train dataframes for the subject 
subjectData <- rbind(subjectTest, subjectTrain)

### Step2 Extracts only the measurements on the mean and standard deviation for each measurement. 

activity <- left_join(activityData, activityLabels, "V1")[,2]

# pivot the featuresNames as column namings for for featuresData columns 
names(featuresData) <- featuresNames$V2

# Create one large Dataset with only these variables: subjectData,  activity,  featuresData
dataAllBind <- cbind(subjectData, activity)
dataAllBind <- cbind(dataAllBind, featuresData)

# Get the mean and std measures for each one.
searchFunction <- grep("mean\\(\\)|std\\(\\)", featuresNames$V2)
splitFeaturesNames <- featuresNames$V2[searchFunction]

datasetColumns <- c("V1", "activity", as.character(splitFeaturesNames))
reducedData <- dataAllBind[, datasetColumns]


### Step3. Uses descriptive activity names to name the activities in the data set
### Step4. Appropriately labels the data set with descriptive variable names. 
names(reducedData)[1] <- "subject"
names(reducedData) <- gsub("Acc", "accelerometer_", names(reducedData))
names(reducedData) <- gsub("^f", "frequency_", names(reducedData))
names(reducedData) <- gsub("Gyro", "gyroscope_", names(reducedData))
names(reducedData) <- gsub("Mag", "magnitude_", names(reducedData))
names(reducedData) <- gsub("^t", "time_", names(reducedData))
names(reducedData) <- gsub("-std()", "STD", names(reducedData))
names(reducedData) <- gsub("-mean()", "MEAN", names(reducedData))



### Step 5. From the data set in step 4, creates a second, independent tidy 
### data set with the average of each variable for each activity and each subject.

# aggregate all other variables of subject and activity columns on the reducedData
# using the mean in order to get the average value. 
tidyDataset <- aggregate(data = reducedData, . ~subject + activity, FUN = mean)
write.table(tidyDataset, "tidyDataset.txt", row.names = FALSE)
