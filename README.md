# run_analysis.R
 It does the following steps using the "Human Activity Recognition Using Smartphones Data Set" 
 1- Merges the training and the test sets to create one data set.
 2- Extracts only the measurements on the mean and standard deviation for each measurement.
 3- Uses descriptive activity names to name the activities in the data set
 4- Appropriately labels the data set with descriptive variable names.
 5- From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
 However, my script doesn't do these steps in order for clarity and performance reasons
 It does these steps in the following order: 3, 2, 4, 1, 5

	### Loading Data:
		(((
		# Load the Training set.
		dataTrain <- read.table("./UCI HAR Dataset/train/X_train.txt")
		
		# Load the test set.
		dataTest <- read.table("./UCI HAR Dataset/test/X_test.txt")
		)))
	### Step 3:
		(((
		# Load the activity labels for the training data, and replace their indices by the activity name 1 <- WALKING, 2 <- WALKING_UPSTAIRS and so on.
		# Give them the column name "labels" which stands for activity labels
		
		labelsTrain <- read.table("./UCI HAR Dataset/train/y_train.txt")
		colnames(labelsTrain) <- c("labels")
		labelsTrain <- mapvalues(labelsTrain$labels, from = c(1, 2, 3, 4, 5, 6), to = c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS", "SITTING","STANDING", "LAYING"))
		
		
		# Load the activity labels for the test data, and replace their indices by the activity name 1 <- WALKING, 2 <- WALKING_UPSTAIRS and so on.
		# Give them the column name "labels" which stands for activity labels
		labelsTest <- read.table("./UCI HAR Dataset/test/y_test.txt")
		colnames(labelsTest) <- c("labels")
		labelsTest <- mapvalues(labelsTest$labels, from = c(1, 2, 3, 4, 5, 6), to = c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS", "SITTING","STANDING", "LAYING"))

		# Load the subjects for the training data and give them the column name "subjects".
		subjectsTrain <- read.table("./UCI HAR Dataset/train/subject_train.txt")
		colnames(subjectsTrain) <- c("subjects")

		# Load the subjects for the test data and give them the column name "subjects".
		subjectsTest <- read.table("./UCI HAR Dataset/test/subject_test.txt")
		colnames(subjectsTest) <- c("subjects")
		
		)))
	### Step 2:
		(((
		# Extracts only the measurements on the mean and standard deviation for each measurement.
		# Load the features from features.txt
		features <- read.table("./UCI HAR Dataset/features.txt")

		# We are only interested in the vector containing the feature names
		featuresVector <- unlist(features$V2)

		# get only the indices of the features containing the pattern "-std()" and the pattern "-mean"
		stdFeaturesVector <- grep(pattern="-std()",x=featuresVector, fixed=TRUE)
		meanFeaturesVector <- grep(pattern="-mean()",x=featuresVector,fixed=TRUE)
		meanFeaturesVector <- unlist(meanFeaturesVector)
		stdFeaturesVector <- unlist(stdFeaturesVector)

		# Combine both vectors in one vector
		stdAndMean <- rbind(meanFeaturesVector, stdFeaturesVector)
		# Sort the indices in ascending order
		stdAndMean <- sort(stdAndMean)

		# Extract the part of the training data that has the mean and standard deviation for each measurement.
		dataTrain <- dataTrain[,stdAndMean]
		)))
		### Step 4:
		(((		
		# Rename columns using features names. 
		colnames(dataTrain) <- featuresVector[stdAndMean]

		# Extract the part of the test data that has the mean and standard deviation for each measurement.
		dataTest <- dataTest[,stdAndMean]

		# Rename columns using features names.
		colnames(dataTest) <- featuresVector[stdAndMean]
		)))
		### Step 1:
		(((
		# Merges the training and the test sets to create one data set.

		# Merge activity labels of the training and test data sets
		labels <- c(labelsTrain,labelsTest)

		# Merge subjects of the training and test data sets
		subjects <- rbind(subjectsTrain,subjectsTest)

		# Merge mean and std measurements of the training and test data sets
		data <- rbind(dataTrain, dataTest)

		#add activity labels and subjects to the measurements to form the complete table
		data$labels <- labels
		data$subjects <- subjects$subjects
		)))
		### Step 5: 
		(((
		# tidy data set with the average of each variable for each activity and each subject.
		dataSummary <- ddply(data, .(labels,subjects), numcolwise(mean))

		#writing the tidy dataset into a file
		write.fwf(x=dataSummary, file = "dataSummary.txt", sep = "\t\t", justify="left", rownames=FALSE)
		)))
	
# Dependencies
 The "Human Activity Recognition using Smartphones Data Set"
 The data set could be downloaded from here: https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
 From this dataset we use the following files
 
 'features.txt': for getting the needed std and mean measurements and getting feature names.
 
 'train/X_train.txt': Training set.
 
 'train/y_train.txt': Training labels.
 
 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30.
 
 'test/X_test.txt': Test set.
 
 'test/y_test.txt': Test labels.
 
 'test/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30.
 
 # CodeBook.md
 A code book that describes the variables, the data, and any transformations or work performed to clean up the data called