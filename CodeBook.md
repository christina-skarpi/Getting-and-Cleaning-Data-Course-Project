# the code book describing the variables

## get the current date of the dataset downloaded just for future reference if needed. 
dateDownload
# the variable keeping the datasetlink 
datasetlink 
# the variable with the zip name of the dataset that will be generated
zipFile
## data frames for features variables to keep the data  for test and train 
featuresTest
featuresTrain
## data frame for features names variable to keep the data 
featuresNames 
## data frames for activity variables to keep the data  for test and train 
activityTest 
activityTrain 
## data frame for activity labels variable to keep the data 
activityLabels 
## data frames for subject variables to keep the data  for test and train 
subjectTest 
subjectTrain 
# data frame for Variable that merges the training and the test sets to create one data set. Merge the test & train dataframes for the features 
featuresData
## data frame for Variable that merges the test & train dataframes for the activity 
activityData 
## data frame for Variable that merges the test & train dataframes for the subject 
subjectData 
# data frame which have been created by a left join of activityData and activityLabels. 
activity 
## data frame keeping the one dataset with only these variables: subjectData,  activity,  featuresData
dataAllBind 
## find the  mean and std measuresfeatures names and keep them in searchFunction, for constructing the splitFeaturesNames data frame
searchFunction 
splitFeaturesNames 
# keep the necessairy dataset columns needed. 
datasetColumns 
# save in the data frame the dataset with the necesairy columns for the final dataset
reducedData 
## data frame in which all tidy dataset is stored.  
tidyDataset 
