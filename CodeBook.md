# Getting and Cleaning Data Final Project

Author: Dimuthu Attanayake

The script run_analysis.R collects and cleans a wearable computing data set collected from the accelerometers from the
Samsung Galaxy S smartphone. It uses the following dataset:https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

1. Downloading and loading data onto R

This step uses RCurl package to download the file to the working directory. It is extracted to a file called **excerData** meaning 
exercise data and unzipped.


2.  Activity and features data from  are assigned to the variables 

* **activityLabels** :  
Data from activity_labels.txt is assigned to this name, with column names with column names "ActivityNo" and "ActivityName". Contains the list of exercises performed.

* **features** : 
Data from features.txt is assigned to this name, with column names "CodeNo" and "featureName". Contains data features selected by the sensor signals.


3. Load test data and assign them to "Test"
* **subjectTest** : Data from subject_test.txt" is assigned, with column name, "subjectNames".

* **Xtest** : Data from X_test.txt is assigned with column name, features$featureName.

* **Ytest** : Data from y_test.txt is assigned with column name, CodeNo.
 
* **Test** : created by merging subjectTest, Xtest and Ytest with **cbind()** function. 


4. Load train data and assign them to "Train"
* **subjectTrain** : Data from subject_train.txt is assigned with column name, "subjectNames".

* **Xtrain** : Data from X_train.txt is assigned with column name, features$featureName.

* **Ytrain** : Data from y_train.txt is assigned  col.names = "CodeNo")
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
         
         
         
         

