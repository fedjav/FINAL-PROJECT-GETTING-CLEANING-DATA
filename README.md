# FINAL-PROJECT-GETTING-CLEANING-DATA


# Download the data:
library(data.table)
fileurl = 'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
if (!file.exists('./UCI HAR Dataset.zip')){
  download.file(fileurl,'./UCI HAR Dataset.zip', mode = 'wb')
  unzip("UCI HAR Dataset.zip", exdir = getwd())
}

# First data is read each file individually and it get's converted into a main data frame
features <- read.csv('./UCI HAR Dataset/features.txt', header = FALSE, sep = ' ')
features <- as.character(features[,2])

data.train.x <- read.table('./UCI HAR Dataset/train/X_train.txt')
data.train.actiion <- read.csv('./UCI HAR Dataset/train/y_train.txt', header = FALSE, sep = ' ')
data.train.subject <- read.csv('./UCI HAR Dataset/train/subject_train.txt',header = FALSE, sep = ' ')

data.train <-  data.frame(data.train.subject, data.train.actiion, data.train.x)
names(data.train) <- c(c('subject', 'actiion'), features)

data.test.x <- read.table('./UCI HAR Dataset/test/X_test.txt')
data.test.actiion <- read.csv('./UCI HAR Dataset/test/y_test.txt', header = FALSE, sep = ' ')
data.test.subject <- read.csv('./UCI HAR Dataset/test/subject_test.txt', header = FALSE, sep = ' ')

data.test <-  data.frame(data.test.subject, data.test.actiion, data.test.x)
names(data.test) <- c(c('subject', 'actiion'), features)

# In order to continue, the merging of the training and testing sets into 1 data set is required and it is called data.main
data.main <- rbind(data.train, data.test)

# Get the mean and standard deviation for each variable measured.
mean_std.select <- grep('mean|std', features)
data.sub <- data.main[,c(1,2,mean_std.select + 2)]

# Use descriptive action names to name the action in the data set
actiion.labels <- read.table('./UCI HAR Dataset/activity_labels.txt', header = FALSE)
actiion.labels <- as.character(actiion.labels[,2])
data.sub$actiion <- actiion.labels[data.sub$actiion]

# It now labels the data set with descriptive action names
name.new <- names(data.sub)
name.new <- gsub("[(][)]", "", name.new)
name.new <- gsub("^t", "TimeDomain_", name.new)
name.new <- gsub("^f", "FrequencyDomain_", name.new)
name.new <- gsub("Acc", "Accelerometer", name.new)
name.new <- gsub("Gyro", "Gyroscope", name.new)
name.new <- gsub("Mag", "Magnitude", name.new)
name.new <- gsub("-mean-", "_Mean_", name.new)
name.new <- gsub("-std-", "_StandardDeviation_", name.new)
name.new <- gsub("-", "_", name.new)
names(data.sub) <- name.new

# From last step, a new and independent dataset is displayed. The average of each action and subject is also included.
data.tidy <- aggregate(data.sub[,3:81], by = list(actiion = data.sub$actiion, subject = data.sub$subject),FUN = mean)
write.table(x = data.tidy, file = "data_tidy.txt", row.names = FALSE)

[data_tidy.txt](https://github.com/fedjav/FINAL-PROJECT-GETTING-CLEANING-DATA/files/6118034/data_tidy.txt)

