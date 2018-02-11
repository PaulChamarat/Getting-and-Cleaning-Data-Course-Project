# Getting-and-Cleaning-Data-Course-Project
This assignment is part of the "Getting and Cleaing Data" class. 


One of the most exciting areas in all of data science right now is wearable computing - see for example this article . 
Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. 
The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S 
smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

1.Merges the training and the test sets to create one data set.
2.Extracts only the measurements on the mean and standard deviation for each measurement.
3.Uses descriptive activity names to name the activities in the data set
4.Appropriately labels the data set with descriptive variable names.
5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


In our work, we wil do separate steps. 

- Download the data file from the links with the following command. Then, unzip the downloaded file
```{r}
fileUrl<-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="dataset.zip",method="curl")
unzip("dataset.zip", exdir = getwd())
```

- Read features from file.
```{r}
features <- read.csv('./UCI HAR Dataset/features.txt', header = FALSE, sep = ' ')
features <- as.character(features[,2])
```

- Read both train and test information from download file. Since the files are separated, we will read file one at a time. At the end, we will add the header into the file. The header will be subject, activity and features.
```{r}
data_train_x <- read.table('./UCI HAR Dataset/train/X_train.txt')
data_train_activity <- read.csv('./UCI HAR Dataset/train/y_train.txt', header = FALSE, sep = ' ')
data_train_subject <- read.csv('./UCI HAR Dataset/train/subject_train.txt',header = FALSE, sep = ' ')
data_train <-  data.frame(data_train_subject, data_train_activity, data_train_x)
names(data_train) <- c(c('subject', 'activity'), features)
data_test_x <- read.table('./UCI HAR Dataset/test/X_test.txt')
data_test_activity <- read.csv('./UCI HAR Dataset/test/y_test.txt', header = FALSE, sep = ' ')
data_test_subject <- read.csv('./UCI HAR Dataset/test/subject_test.txt', header = FALSE, sep = ' ')
data_test <-  data.frame(data_test_subject, data_test_activity, data_test_x)
names(data_test) <- c(c('subject', 'activity'), features)
```

Then, we will follow the instruction from the assignment.
1. Merges the training and the test sets to create one data set.
```{r}
data_all<-rbind(data_train,data_test)
```

2. Extracts only the measurements on the mean and standard deviation for each measurement.
```{r}
mean_std_select <- grep('mean|std', features) ## identify the index to have the word mean or std
data_sub <- data_all[,c(1,2,mean_std_select + 2)] ### +2 because the 1st col is subject and 2nd col is activity. 
```

3.Uses descriptive activity names to name the activities in the data set
```{r}
activity_labels <- read.table('./UCI HAR Dataset/activity_labels.txt', header = FALSE)
activity_labels <- as.character(activity_labels[,2])
data_sub$activity <- activity_labels[data_sub$activity]
```

4.Appropriately labels the data set with descriptive variable names.
```{r}
name_new <- names(data_sub)
name_new <- gsub("[(][)]", "", name_new)
name_new <- gsub("^t", "Time_", name_new)
name_new <- gsub("^f", "Frequency_", name_new)
name_new <- gsub("Acc", "Accelerometer", name_new)
name_new <- gsub("Gyro", "Gyroscope", name_new)
name_new <- gsub("Mag", "Magnitude", name_new)
name_new <- gsub("-mean-", "-Mean-", name_new)
name_new <- gsub("-std-", "-StandardDeviation-", name_new)
name_new <- gsub("-meanFreq-", "-MeanFrequency-", name_new)
name_new <- gsub("-mean$", "-Mean", name.new)
name_new <- gsub("-std$", "-StandardDeviation", name_new)
name_new <- gsub("-meanFreq$", "-MeanFrequency", name_new)
name_new <- gsub("-", "_", name_new)
names(data_sub) <- name_new
```

5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
```{r}
data_tidy <- aggregate(data_sub[,3:81], by = list(activity = data_sub$activity, subject = data_sub$subject),FUN = mean)
```



