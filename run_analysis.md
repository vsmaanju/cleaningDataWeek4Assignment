Preliminaries
===================

Load packages.


```r
packages <- c("data.table", "reshape2")
sapply(packages, require, character.only=TRUE, quietly=TRUE)
```

```
## Warning: package 'data.table' was built under R version 3.0.3
```

```
## data.table 1.9.4  For help type: ?data.table
## *** NB: by=.EACHI is now explicit. See README to restore previous behaviour.
```

```
## Warning: package 'reshape2' was built under R version 3.0.3
```

```
## data.table   reshape2 
##       TRUE       TRUE
```

Set path.


```r
path <- getwd()
path
```

```
## [1] "D:/r_learn/data_scientist/Data_cleaning/week4"
```

Get the data
---------------------------


Download the file. Put it in the Data folder. This was already done on 2014-04-11; save time and don't evaluate again.


```r
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
f <- "Dataset.zip"
if (!file.exists(path)) {dir.create(path)}
download.file(url, file.path(path, f))
```
Unzip the file. This was already done on 2014-04-11; save time and don't evaluate again.

```r
executable <- file.path("C:", "Program Files (x86)", "7-Zip", "7z.exe")
parameters <- "x"
cmd <- paste(paste0("\"", executable, "\""), parameters, paste0("\"", file.path(path, f), "\""))
system(cmd)
```

The archive put the files in a folder named `UCI HAR` Dataset. Set this folder as the input path. List the files here.

```r
pathIn <- file.path(path, "UCI HAR Dataset")
list.files(pathIn, recursive=TRUE)
```

```
## character(0)
```
See the README.txt file in r path for detailed information on the dataset.

For the purposes of this project, the files in the Inertial Signals folders are not used.

Read the files
-----------------------

Read the subject files.

```r
dtSubjectTrain <- fread(file.path(pathIn, "train", "subject_train.txt"))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/train/subject_train.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a0e8a7ff8' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "train", "subject_train.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a0e8a7ff8
```

```r
dtSubjectTest  <- fread(file.path(pathIn, "test" , "subject_test.txt" ))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/test/subject_test.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a02fbd536' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "test", "subject_test.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a02fbd536
```
Read the activity files. For some reason, these are called label files in the README.txt documentation.

```r
dtActivityTrain <- fread(file.path(pathIn, "train", "Y_train.txt"))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/train/Y_train.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a07d9541e9' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "train", "Y_train.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a07d9541e9
```

```r
dtActivityTest  <- fread(file.path(pathIn, "test" , "Y_test.txt" ))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/test/Y_test.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a012fd2e55' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "test", "Y_test.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a012fd2e55
```
Read the data files. fread seems to be giving me some trouble reading files. Using a helper function, read the file with read.table instead, then convert the resulting data frame to a data table. Return the data table.


```r
fileToDataTable <- function (f) {
    df <- read.table(f)
    dt <- data.table(df)
}
dtTrain <- fileToDataTable(file.path(pathIn, "train", "X_train.txt"))
```

```
## Warning in file(file, "rt"): cannot open file
## 'D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/train/X_train.txt': No such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

```r
dtTest  <- fileToDataTable(file.path(pathIn, "test" , "X_test.txt" ))
```

```
## Warning in file(file, "rt"): cannot open file
## 'D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/test/X_test.txt': No such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```
Merge the training and the test sets
--------------

Concatenate the data tables.

```r
dtSubject <- rbind(dtSubjectTrain, dtSubjectTest)
```

```
## Error in rbind(dtSubjectTrain, dtSubjectTest): object 'dtSubjectTrain' not found
```

```r
setnames(dtSubject, "V1", "subject")
```

```
## Error in is.data.frame(x): object 'dtSubject' not found
```

```r
dtActivity <- rbind(dtActivityTrain, dtActivityTest)
```

```
## Error in rbind(dtActivityTrain, dtActivityTest): object 'dtActivityTrain' not found
```

```r
setnames(dtActivity, "V1", "activityNum")
```

```
## Error in is.data.frame(x): object 'dtActivity' not found
```

```r
dt <- rbind(dtTrain, dtTest)
```

```
## Error in rbind(dtTrain, dtTest): object 'dtTrain' not found
```
Merge columns.

```r
dtSubject <- cbind(dtSubject, dtActivity)
```

```
## Error in cbind(dtSubject, dtActivity): object 'dtSubject' not found
```

```r
dt <- cbind(dtSubject, dt)
```

```
## Error in cbind(dtSubject, dt): object 'dtSubject' not found
```
Set key.

```r
setkey(dt, subject, activityNum)
```

```
## Error in setkeyv(x, cols, verbose = verbose, physical = physical): x is not a data.table
```
Extract only the mean and standard deviation
-------------------------

Read the `features.txt` file. This tells which variables in `dt` are measurements for the mean and standard deviation.

```r
dtFeatures <- fread(file.path(pathIn, "features.txt"))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/features.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a0286772e2' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "features.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a0286772e2
```

```r
setnames(dtFeatures, names(dtFeatures), c("featureNum", "featureName"))
```

```
## Error in is.data.frame(x): object 'dtFeatures' not found
```
Subset only measurements for the mean and standard deviation.

```r
dtFeatures <- dtFeatures[grepl("mean\\(\\)|std\\(\\)", featureName)]
```

```
## Error in eval(expr, envir, enclos): object 'dtFeatures' not found
```
Convert the column numbers to a vector of variable names matching columns in dt.

```r
dtFeatures$featureCode <- dtFeatures[, paste0("V", featureNum)]
```

```
## Error in eval(expr, envir, enclos): object 'dtFeatures' not found
```

```r
head(dtFeatures)
```

```
## Error in head(dtFeatures): object 'dtFeatures' not found
```

```r
dtFeatures$featureCode
```

```
## Error in eval(expr, envir, enclos): object 'dtFeatures' not found
```
Subset these variables using variable names.

```r
select <- c(key(dt), dtFeatures$featureCode)
```

```
## Error in eval(expr, envir, enclos): object 'dtFeatures' not found
```

```r
dt <- dt[, select, with=FALSE]
```

```
## Error in eval(expr, envir, enclos): object 'select' not found
```
Use descriptive activity names
----------------------

Read `activity_labels.txt` file. This will be used to add descriptive names to the activities.

```r
dtActivityNames <- fread(file.path(pathIn, "activity_labels.txt"))
```

```
## Warning in shell(paste("(", input, ") > ", tt, sep = "")):
## '(D:/r_learn/data_scientist/Data_cleaning/week4/UCI HAR
## Dataset/activity_labels.txt) >
## C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a02148442c' execution
## failed with error code 127
```

```
## Error in fread(file.path(pathIn, "activity_labels.txt")): File not found: C:\Users\vsingh\AppData\Local\Temp\RtmpqkbScZ\file9a02148442c
```

```r
setnames(dtActivityNames, names(dtActivityNames), c("activityNum", "activityName"))
```

```
## Error in is.data.frame(x): object 'dtActivityNames' not found
```
Label with descriptive activity names
-----------------------------


Merge activity labels.

```r
dt <- merge(dt, dtActivityNames, by="activityNum", all.x=TRUE)
```

```
## Error: C stack usage is too close to the limit
```
Add activityName as a key.

```r
setkey(dt, subject, activityNum, activityName)
```

```
## Error in setkeyv(x, cols, verbose = verbose, physical = physical): x is not a data.table
```
Melt the data table to reshape it from a short and wide format to a tall and narrow format.

```r
dt <- data.table(melt(dt, key(dt), variable.name="featureCode"))
```
Merge activity name.

```r
dt <- merge(dt, dtFeatures[, list(featureNum, featureCode, featureName)], by="featureCode", all.x=TRUE)
```

```
## Error in merge.data.table(dt, dtFeatures[, list(featureNum, featureCode, : object 'dtFeatures' not found
```
Create a new variable, activity that is equivalent to activityName as a factor class. Create a new variable, feature that is equivalent to featureName as a factor class.

```r
dt$activity <- factor(dt$activityName)
```

```
## Error in `[<-.data.table`(x, j = name, value = value): RHS of assignment to new column 'activity' is zero length but not empty list(). For new columns the RHS must either be empty list() to create an empty list column, or, have length > 0; e.g. NA_integer_, 0L, etc.
```

```r
dt$feature <- factor(dt$featureName)
```

```
## Error in `[<-.data.table`(x, j = name, value = value): RHS of assignment to new column 'feature' is zero length but not empty list(). For new columns the RHS must either be empty list() to create an empty list column, or, have length > 0; e.g. NA_integer_, 0L, etc.
```
Seperate features from featureName using the helper function grepthis.

```r
grepthis <- function (regex) {
  grepl(regex, dt$feature)
}
## Features with 2 categories
n <- 2
y <- matrix(seq(1, n), nrow=n)
x <- matrix(c(grepthis("^t"), grepthis("^f")), ncol=nrow(y))
dt$featDomain <- factor(x %*% y, labels=c("Time", "Freq"))
```

```
## Error in factor(x %*% y, labels = c("Time", "Freq")): invalid 'labels'; length 2 should be 1 or 0
```

```r
x <- matrix(c(grepthis("Acc"), grepthis("Gyro")), ncol=nrow(y))
dt$featInstrument <- factor(x %*% y, labels=c("Accelerometer", "Gyroscope"))
```

```
## Error in factor(x %*% y, labels = c("Accelerometer", "Gyroscope")): invalid 'labels'; length 2 should be 1 or 0
```

```r
x <- matrix(c(grepthis("BodyAcc"), grepthis("GravityAcc")), ncol=nrow(y))
dt$featAcceleration <- factor(x %*% y, labels=c(NA, "Body", "Gravity"))
```

```
## Error in factor(x %*% y, labels = c(NA, "Body", "Gravity")): invalid 'labels'; length 3 should be 1 or 0
```

```r
x <- matrix(c(grepthis("mean()"), grepthis("std()")), ncol=nrow(y))
dt$featVariable <- factor(x %*% y, labels=c("Mean", "SD"))
```

```
## Error in factor(x %*% y, labels = c("Mean", "SD")): invalid 'labels'; length 2 should be 1 or 0
```

```r
## Features with 1 category
dt$featJerk <- factor(grepthis("Jerk"), labels=c(NA, "Jerk"))
```

```
## Error in factor(grepthis("Jerk"), labels = c(NA, "Jerk")): invalid 'labels'; length 2 should be 1 or 0
```

```r
dt$featMagnitude <- factor(grepthis("Mag"), labels=c(NA, "Magnitude"))
```

```
## Error in factor(grepthis("Mag"), labels = c(NA, "Magnitude")): invalid 'labels'; length 2 should be 1 or 0
```

```r
## Features with 3 categories
n <- 3
y <- matrix(seq(1, n), nrow=n)
x <- matrix(c(grepthis("-X"), grepthis("-Y"), grepthis("-Z")), ncol=nrow(y))
dt$featAxis <- factor(x %*% y, labels=c(NA, "X", "Y", "Z"))
```

```
## Error in factor(x %*% y, labels = c(NA, "X", "Y", "Z")): invalid 'labels'; length 4 should be 1 or 0
```
Check to make sure all possible combinations of feature are accounted for by all possible combinations of the factor class variables.

```r
r1 <- nrow(dt[, .N, by=c("feature")])
```

```
## Error in eval(expr, envir, enclos): object 'feature' not found
```

```r
r2 <- nrow(dt[, .N, by=c("featDomain", "featAcceleration", "featInstrument", "featJerk", "featMagnitude", "featVariable", "featAxis")])
```

```
## Error in eval(expr, envir, enclos): object 'featDomain' not found
```

```r
r1 == r2
```

```
## Error in eval(expr, envir, enclos): object 'r1' not found
```
Yes, I accounted for all possible combinations. feature is now redundant.

Create a tidy data set
-------

Create a data set with the average of each variable for each activity and each subject.

```r
setkey(dt, subject, activity, featDomain, featAcceleration, featInstrument, featJerk, featMagnitude, featVariable, featAxis)
```

```
## Error in setkeyv(x, cols, verbose = verbose, physical = physical): some columns are not in the data.table: subject,activity,featDomain,featAcceleration,featInstrument,featJerk,featMagnitude,featVariable,featAxis
```

```r
dtTidy <- dt[, list(count = .N, average = mean(value)), by=key(dt)]
```

```
## Error in `[.data.table`(dt, , list(count = .N, average = mean(value)), : by=c(...), key(...) or names(...) must evaluate to 'character'
```
Make codebook.

```r
knit("makeCodebook.Rmd", output="codebook.md", encoding="ISO8859-1", quiet=TRUE)
```

```
## [1] "codebook.md"
```

```r
markdownToHTML("codebook.md", "codebook.html")
```
