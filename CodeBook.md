# Getting and Cleaning Data Final Project

Author: Dimuthu Attanayake

The script run_analysis.R collects and cleans a wearable computing data set collected from the accelerometers from the
Samsung Galaxy S smartphone. It uses the following dataset:https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

1. Downloading and loading data onto R

This step uses RCurl package to download the file to the working directory. It is extracted to a file called excerData meaning 
exercise data and unzipped.


2.  Activity and  features data are 
activityLabels <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("ActivityNo","ActivityName"))
activityLabels
features <- read.table("UCI HAR Dataset/features.txt", col.names = c("CodeNo","featureName"))
head(features)

#load test data and assign them to "Test"
subjectTest <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subjectNames")
subjectTest
Xtest <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$featureName )
head(Xtest)
Ytest <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "CodeNo")
head(Ytest) 
Test <- cbind(subjectTest,Xtest,Ytest)


#load train data and assign them to "Train"
subjectTrain <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subjectNames")
subjectTrain
Xtrain <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$featureName)
Xtrain
Ytrain <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "CodeNo")
head(Ytrain)
Train <- cbind(subjectTrain, Xtrain, Ytrain)

# 1. Merges the training and the test sets to create one data set.

mergeData <- rbind(Test,Train)

#2.Extracts only the measurements on the mean and standard deviation for each measurement.

library(dplyr)
mean_sd <- mergeData %>% select(subjectNames, CodeNo, contains("mean"), contains("std"))


# 3.Uses descriptive activity names to name the activities in the data set

descriptiveNames <- activityLabels[mean_sd$CodeNo, 2]
         
         
#4.Appropriately labels the data set with descriptive variable names.
         
names(mergeData)[2] = "activityNames"
names(mergeData) <- gsub("BodyBody", "Body",gsub("^t", "Time",gsub("^f","Frequency",gsub("Mag", "Magnitude", names(mergeData)))))
         
         
#5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
         
FinalData <- mergeData %>%
             group_by(subjectNames, activityNames) %>%
             summarise_all(funs(mean))
         
write.table(FinalData, "FinalData.txt", row.name=FALSE)
         
str(FinalData)
         
         
         
         

