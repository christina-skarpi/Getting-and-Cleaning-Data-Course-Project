#!/usr/bin/env Rscript


## in order to run the run_analysis.R script you would need to change the working directory, to your personal working directory before running the project. 
## setwd("YOUR_ABSOLUTE_PATH_TO_THE_run_analysis.R_Script")


## Below you can find more info on each and every step of the script. 

## Load the nesesairy libraries
library(data.table)
library(dplyr)


## Start reading files
setwd("C:/Users/skarpath/Documents/Rproject")

## get the current date of the dataset downloaded just for future reference if needed. 
dateDownload <- date()

# Download the datasets from the link provided and unzip them
datasetlink <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
zipFile <- "dataset.zip"
if (!file.exists(zipFile)){
  download.file(datasetlink, destfile = zipFile, mode='wb')
}
if (!file.exists("./UCI HAR Dataset")){
  unzip(zipFile)
}

## Read the features files test and train to a dataframe
featuresTest <- read.table("./UCI HAR Dataset/test/X_test.txt", header = F)
featuresTrain <- read.table("./UCI HAR Dataset/train/X_train.txt", header = F)

## Read the features names test and train to a dataframe
featuresNames <- read.table("./UCI HAR Dataset/features.txt", header = F)

## Read the activity files test and train to a dataframe
activityTest <- read.table("./UCI HAR Dataset/test/y_test.txt", header = F)
activityTrain <- read.table("./UCI HAR Dataset/train/y_train.txt", header = F)

## Read the activity labels test and train to a dataframe
activityLabels <- read.table("./UCI HAR Dataset/activity_labels.txt", header = F)

## Read the subject files test and train to a dataframe
subjectTest <- read.table("./UCI HAR Dataset/test/subject_test.txt", header = F)
subjectTrain <- read.table("./UCI HAR Dataset/train/subject_train.txt", header = F)

# Step1 Merges the training and the test sets to create one data set. Merge the test & train dataframes for the features 
featuresData <- rbind(featuresTest, featuresTrain)

## Merge the test & train dataframes for the activity 
activityData <- rbind(activityTest, activityTrain)

## Merge the test & train dataframes for the subject 
subjectData <- rbind(subjectTest, subjectTrain)

# Step2 Extracts only the measurements on the mean and standard deviation for each measurement. 

activity <- left_join(activityData, activityLabels, "V1")[,2]

## pivot the featuresNames as column namings for for featuresData columns 
names(featuresData) <- featuresNames$V2

## Create one large Dataset with only these variables: subjectData,  activity,  featuresData
dataAllBind <- cbind(subjectData, activity)
dataAllBind <- cbind(dataAllBind, featuresData)

## Get the mean and std measures for each one.
searchFunction <- grep("mean\\(\\)|std\\(\\)", featuresNames$V2)
splitFeaturesNames <- featuresNames$V2[searchFunction]

datasetColumns <- c("V1", "activity", as.character(splitFeaturesNames))
reducedData <- dataAllBind[, datasetColumns]


# Step3. Uses descriptive activity names to name the activities in the data set
# Step4. Appropriately labels the data set with descriptive variable names. 
names(reducedData)[1] <- "subject"
names(reducedData) <- gsub("Acc", "accelerometer_", names(reducedData))
names(reducedData) <- gsub("^f", "frequency_", names(reducedData))
names(reducedData) <- gsub("Gyro", "gyroscope_", names(reducedData))
names(reducedData) <- gsub("Mag", "magnitude_", names(reducedData))
names(reducedData) <- gsub("^t", "time_", names(reducedData))
names(reducedData) <- gsub("-std.+-", "STD", names(reducedData))
names(reducedData) <- gsub("-mean.+-", "MEAN", names(reducedData))



# Step 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## aggregate all other variables of subject and activity columns on the reducedData
## using the mean in order to get the average value. 
tidyDataset <- aggregate(data = reducedData, . ~subject + activity, FUN = mean)
write.table(tidyDataset, "tidyDataset.txt", row.names = FALSE)
