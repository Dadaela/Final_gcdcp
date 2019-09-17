
# Final_gcdcp
######################################################
######################################################
#Readme.md
######################################################
######################################################

Getting and Cleaning Data Course Project
Dataset
Human Activity Recognition Using Smartphones
"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

Files
CodeBook.md is the code book that describes how the variables, the data, have been cleaned up

run_analysis.R allows us to prepare the data file as required by the project:
1-Merges the training and the test sets to create one data set.
2-Extracts only the measurements on the mean and standard deviation for each measurement.
3-Uses descriptive activity names to name the activities in the data set
4-Appropriately labels the data set with descriptive variable names.
5-From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

FinalData.txt is the exported final data after performing all required steps

######################################################
######################################################
#Run_Analysis.R
######################################################
######################################################

library(dplyr)

filename <- "Coursera_gcdcp_Final.zip"

#Dowload dataset
if (!file.exists(filename)){
  fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
  download.file(fileURL, filename, method="curl")
}  

# Check folder 
if (!file.exists("UCI HAR Dataset")) { 
  unzip(filename) 
}

#read_table
features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))
activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")

subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")

#Merges the training and the test sets to create one data set.
X <- rbind(x_train, x_test)
dim(X)
#merge Y train e test
Y <- rbind(y_train, y_test)
dim(Y)
#merge Subject train e test
Subject <- rbind(subject_train, subject_test)

#total MErge
Merged_Data <- cbind(Subject, Y, X)


#Extracts only the measurements on the mean and standard deviation for each measurement.
TidyData <- Merged_Data %>% select(subject, code, contains("mean"), contains("std"))

#Uses descriptive activity names to name the activities in the data set
Data_all$code <- activity_labels[Data_all$code, 2]

#Appropriately labels the data set with descriptive variable names.
names(TidyData)[2] = "activity"
names(TidyData)<-gsub("Acc", "Accelerometer", names(TidyData))
names(TidyData)<-gsub("Gyro", "Gyroscope", names(TidyData))
names(TidyData)<-gsub("BodyBody", "Body", names(TidyData))
names(TidyData)<-gsub("Mag", "Magnitude", names(TidyData))
names(TidyData)<-gsub("^t", "Time", names(TidyData))
names(TidyData)<-gsub("^f", "Frequency", names(TidyData))
names(TidyData)<-gsub("tBody", "TimeBody", names(TidyData))
names(TidyData)<-gsub("-mean()", "Mean", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-std()", "STD", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-freq()", "Frequency", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("angle", "Angle", names(TidyData))
names(TidyData)<-gsub("gravity", "Gravity", names(TidyData))

#From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.merge X train e test
FinalData <- TidyData %>%
  group_by(subject, activity) %>%
  summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)

str(FinalData)
View(FinalData)

######################################################
######################################################
#Code Book
######################################################
######################################################

The run_analysis.R script allows us to prepare the data file as required by the project.

1.Download the dataset
	 Dataset downloaded and extracted under the folder called UCI Human Activity Recognition Using Smartphones
	"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

2.Assign each data to variables
	features       <- features.txt           :dim --> rows=561 , columns=2   -->select variable  
	activity_labels<- activity_labels.txt    :dim --> rows=6   , columns=2   -->List of activities (labels)
	subject_test   <- test/subject_test.txt  :dim --> rows=2947, column=1    -->contains test data subjects being observed
	subject_train  <- test/subject_train.txt :dim --> rows=7352, column=1    -->contains train data subjects being observed
	x_test         <- test/X_test.txt        :dim --> rows=2947, columns=561 -->contains recorded features test data
	x_train        <- test/X_train.txt       :dim --> rows=7352, columns=561 -->contains recorded features train data
	y_test         <- test/y_test.txt        :dim --> rows=2947, column=1    -->contains test data of activities’code labels
	y_train        <- test/y_train.txt       :dim --> rows=7352, column=1    -->contains train data of activities’code labels

3.Merges the training and the test sets to create one data set.
	Merges the training and the test sets to create one data set using rbind() function
	Subject==>rows=10299, column=1    and is created by merging subject_train&subject_test 
	X      ==>rows=10299, columns=561 and is created by merging x_train&x_test 
	Y      ==>rows=10299, column=1    and is created by merging y_train&y_test

	Merge the Subject X Y
	Merged_Data==>rows=10299, column=563) is created using cbind() function

4.Extracts only the measurements on the mean and standard deviation for each measurement
	TidyData ==>rows=10299, columns=88 is created by subsetting Merged_Data, selecting only columns: subject, code and the measurements on the mean std

5.Uses descriptive activity names to name the activities in the data set
	Replacing codes column of the TidyData with value labels of the activities variable

6.Appropriately labels the data set with descriptive variable names
	code column in TidyData renamed into activities
	All Acc in column’s name replaced by Accelerometer
	All Gyro in column’s name replaced by Gyroscope
	All BodyBody in column’s name replaced by Body
	All Mag in column’s name replaced by Magnitude
	All start with character f in column’s name replaced by Frequency
	All start with character t in column’s name replaced by Time

7.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
	FinalData (rows=180, columns=88) is created by sumarizing TidyData taking the means of each variable for each activity and each subject, 
	after groupped by subject and activity.
	Export FinalData into FinalData.txt file.




