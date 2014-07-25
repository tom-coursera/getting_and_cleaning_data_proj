
# README file for Class Project

## Part 1: merging the training and test sets

The training and test data were read into R using the following code:


```r
    library(plyr) ; library(htmltools) ; library(yaml)
    library(rmarkdown) ; library(knitr)

    subject_train <- read.table("./train/subject_train.txt")
    x_train <- read.table("./train/X_train.txt")
    y_train <- read.table("./train/y_train.txt")
    
    subject_test <- read.table("./test/subject_test.txt")
    x_test <- read.table("./test/X_test.txt")
    y_test <- read.table("./test/y_test.txt")
```

There are 7352 rows in each "train" data file and 2947 records in each "test" data file.  There are 561 columns in the "x_train.txt" and "x_test.txt" data files.  The columns are merged together as follows:


```r
    df_train <- cbind(subject_train, x_train, y_train)
    df_test <- cbind(subject_test, x_test, y_test)
```

Next, the rows from "train" and "test" data sets are merged:


```r
    df <- rbind(df_train, df_test)
```

Column names for the merged data frame are assigned using the code below:


```r
    x_labels <- read.table("./features.txt")
    colnames(df) <- c("subject", as.character(x_labels[,2]), "activity")
```

## Part 3: Using descriptive activity names to name the activities in the data set

I performed part 3 before part 2 because it seems to be a more logical order for me. The descriptive activity names are loaded using:

```r
    y_labels <- read.table("./activity_labels.txt")
    y_labels
```

```
##   V1                 V2
## 1  1            WALKING
## 2  2   WALKING_UPSTAIRS
## 3  3 WALKING_DOWNSTAIRS
## 4  4            SITTING
## 5  5           STANDING
## 6  6             LAYING
```
The descriptive activity names are appended as a column to the data frame using:


```r
    for (row in 1:dim(y_labels)[[1]]) {
         df[df$activity == y_labels[row, 1], "descriptive_activity_label"] <- y_labels[row, 2]
    }
```
The results are shown below for the first and last 5 rows of the data frame

```r
head(df[,c("subject", "activity", "descriptive_activity_label")],5)
```

```
##   subject activity descriptive_activity_label
## 1       1        5                   STANDING
## 2       1        5                   STANDING
## 3       1        5                   STANDING
## 4       1        5                   STANDING
## 5       1        5                   STANDING
```

```r
tail(df[,c("subject", "activity", "descriptive_activity_label")],5)
```

```
##       subject activity descriptive_activity_label
## 10295      24        2           WALKING_UPSTAIRS
## 10296      24        2           WALKING_UPSTAIRS
## 10297      24        2           WALKING_UPSTAIRS
## 10298      24        2           WALKING_UPSTAIRS
## 10299      24        2           WALKING_UPSTAIRS
```

The data frame has 10299 rows and 564 columns.

## Part 2: Extracting only the measurements on the mean and standard deviation

This part is accomplished by searching for column names containing the strings "mean" and "std".  Those column names which we want to keep are shown below:


```r
    grep("activity|^subject$", names(df), value=TRUE)
```

```
## [1] "subject"                    "activity"                  
## [3] "descriptive_activity_label"
```

```r
    grep("mean|meanFreq", names(df), value=TRUE)
```

```
##  [1] "tBodyAcc-mean()-X"               "tBodyAcc-mean()-Y"              
##  [3] "tBodyAcc-mean()-Z"               "tGravityAcc-mean()-X"           
##  [5] "tGravityAcc-mean()-Y"            "tGravityAcc-mean()-Z"           
##  [7] "tBodyAccJerk-mean()-X"           "tBodyAccJerk-mean()-Y"          
##  [9] "tBodyAccJerk-mean()-Z"           "tBodyGyro-mean()-X"             
## [11] "tBodyGyro-mean()-Y"              "tBodyGyro-mean()-Z"             
## [13] "tBodyGyroJerk-mean()-X"          "tBodyGyroJerk-mean()-Y"         
## [15] "tBodyGyroJerk-mean()-Z"          "tBodyAccMag-mean()"             
## [17] "tGravityAccMag-mean()"           "tBodyAccJerkMag-mean()"         
## [19] "tBodyGyroMag-mean()"             "tBodyGyroJerkMag-mean()"        
## [21] "fBodyAcc-mean()-X"               "fBodyAcc-mean()-Y"              
## [23] "fBodyAcc-mean()-Z"               "fBodyAcc-meanFreq()-X"          
## [25] "fBodyAcc-meanFreq()-Y"           "fBodyAcc-meanFreq()-Z"          
## [27] "fBodyAccJerk-mean()-X"           "fBodyAccJerk-mean()-Y"          
## [29] "fBodyAccJerk-mean()-Z"           "fBodyAccJerk-meanFreq()-X"      
## [31] "fBodyAccJerk-meanFreq()-Y"       "fBodyAccJerk-meanFreq()-Z"      
## [33] "fBodyGyro-mean()-X"              "fBodyGyro-mean()-Y"             
## [35] "fBodyGyro-mean()-Z"              "fBodyGyro-meanFreq()-X"         
## [37] "fBodyGyro-meanFreq()-Y"          "fBodyGyro-meanFreq()-Z"         
## [39] "fBodyAccMag-mean()"              "fBodyAccMag-meanFreq()"         
## [41] "fBodyBodyAccJerkMag-mean()"      "fBodyBodyAccJerkMag-meanFreq()" 
## [43] "fBodyBodyGyroMag-mean()"         "fBodyBodyGyroMag-meanFreq()"    
## [45] "fBodyBodyGyroJerkMag-mean()"     "fBodyBodyGyroJerkMag-meanFreq()"
```

```r
    grep("std", names(df), value=TRUE)
```

```
##  [1] "tBodyAcc-std()-X"           "tBodyAcc-std()-Y"          
##  [3] "tBodyAcc-std()-Z"           "tGravityAcc-std()-X"       
##  [5] "tGravityAcc-std()-Y"        "tGravityAcc-std()-Z"       
##  [7] "tBodyAccJerk-std()-X"       "tBodyAccJerk-std()-Y"      
##  [9] "tBodyAccJerk-std()-Z"       "tBodyGyro-std()-X"         
## [11] "tBodyGyro-std()-Y"          "tBodyGyro-std()-Z"         
## [13] "tBodyGyroJerk-std()-X"      "tBodyGyroJerk-std()-Y"     
## [15] "tBodyGyroJerk-std()-Z"      "tBodyAccMag-std()"         
## [17] "tGravityAccMag-std()"       "tBodyAccJerkMag-std()"     
## [19] "tBodyGyroMag-std()"         "tBodyGyroJerkMag-std()"    
## [21] "fBodyAcc-std()-X"           "fBodyAcc-std()-Y"          
## [23] "fBodyAcc-std()-Z"           "fBodyAccJerk-std()-X"      
## [25] "fBodyAccJerk-std()-Y"       "fBodyAccJerk-std()-Z"      
## [27] "fBodyGyro-std()-X"          "fBodyGyro-std()-Y"         
## [29] "fBodyGyro-std()-Z"          "fBodyAccMag-std()"         
## [31] "fBodyBodyAccJerkMag-std()"  "fBodyBodyGyroMag-std()"    
## [33] "fBodyBodyGyroJerkMag-std()"
```

The code to create a sub-data frame containing only the columns of interest is shown below:


```r
    keep0 <- grep("activity|^subject$", names(df))
    keep1 <- grep("mean|meanFreq", names(df))
    keep2 <- grep("std", names(df))
    sub_df <- df[, c(keep0, keep1, keep2)]
```

This new data frame now has 10299 rows as before but with only 82 columns.

## Part 4: Appropriately labeling the data set with descriptive variable names

I think the existing column names from the data authors are already quite descriptive without being too long.  For this part of the project, I simply performed some cosmetic changes such as changing "t" to "time_", "f" to "freq_", "BodyBody" to "Body", "-" to "_" (as some software may interpret "-" as minus sign) and deleting all occurrences of "()".  The code and list of new column names are shown below.


```r
    old_column_names <- names(sub_df)
    column_names <- names(sub_df)
    for (each in 1:length(column_names)) {
        column_names[each] <- sub("^t", "time_", column_names[each])
        column_names[each] <- sub("^f", "freq_", column_names[each])
        column_names[each] <- sub("BodyBody", "Body", column_names[each])
        column_names[each] <- sub("\\(\\)", "", column_names[each])
        column_names[each] <- gsub("-", "_", column_names[each])
    }
    names(sub_df) <- column_names
    names(sub_df)
```

```
##  [1] "subject"                       "activity"                     
##  [3] "descriptive_activity_label"    "time_BodyAcc_mean_X"          
##  [5] "time_BodyAcc_mean_Y"           "time_BodyAcc_mean_Z"          
##  [7] "time_GravityAcc_mean_X"        "time_GravityAcc_mean_Y"       
##  [9] "time_GravityAcc_mean_Z"        "time_BodyAccJerk_mean_X"      
## [11] "time_BodyAccJerk_mean_Y"       "time_BodyAccJerk_mean_Z"      
## [13] "time_BodyGyro_mean_X"          "time_BodyGyro_mean_Y"         
## [15] "time_BodyGyro_mean_Z"          "time_BodyGyroJerk_mean_X"     
## [17] "time_BodyGyroJerk_mean_Y"      "time_BodyGyroJerk_mean_Z"     
## [19] "time_BodyAccMag_mean"          "time_GravityAccMag_mean"      
## [21] "time_BodyAccJerkMag_mean"      "time_BodyGyroMag_mean"        
## [23] "time_BodyGyroJerkMag_mean"     "freq_BodyAcc_mean_X"          
## [25] "freq_BodyAcc_mean_Y"           "freq_BodyAcc_mean_Z"          
## [27] "freq_BodyAcc_meanFreq_X"       "freq_BodyAcc_meanFreq_Y"      
## [29] "freq_BodyAcc_meanFreq_Z"       "freq_BodyAccJerk_mean_X"      
## [31] "freq_BodyAccJerk_mean_Y"       "freq_BodyAccJerk_mean_Z"      
## [33] "freq_BodyAccJerk_meanFreq_X"   "freq_BodyAccJerk_meanFreq_Y"  
## [35] "freq_BodyAccJerk_meanFreq_Z"   "freq_BodyGyro_mean_X"         
## [37] "freq_BodyGyro_mean_Y"          "freq_BodyGyro_mean_Z"         
## [39] "freq_BodyGyro_meanFreq_X"      "freq_BodyGyro_meanFreq_Y"     
## [41] "freq_BodyGyro_meanFreq_Z"      "freq_BodyAccMag_mean"         
## [43] "freq_BodyAccMag_meanFreq"      "freq_BodyAccJerkMag_mean"     
## [45] "freq_BodyAccJerkMag_meanFreq"  "freq_BodyGyroMag_mean"        
## [47] "freq_BodyGyroMag_meanFreq"     "freq_BodyGyroJerkMag_mean"    
## [49] "freq_BodyGyroJerkMag_meanFreq" "time_BodyAcc_std_X"           
## [51] "time_BodyAcc_std_Y"            "time_BodyAcc_std_Z"           
## [53] "time_GravityAcc_std_X"         "time_GravityAcc_std_Y"        
## [55] "time_GravityAcc_std_Z"         "time_BodyAccJerk_std_X"       
## [57] "time_BodyAccJerk_std_Y"        "time_BodyAccJerk_std_Z"       
## [59] "time_BodyGyro_std_X"           "time_BodyGyro_std_Y"          
## [61] "time_BodyGyro_std_Z"           "time_BodyGyroJerk_std_X"      
## [63] "time_BodyGyroJerk_std_Y"       "time_BodyGyroJerk_std_Z"      
## [65] "time_BodyAccMag_std"           "time_GravityAccMag_std"       
## [67] "time_BodyAccJerkMag_std"       "time_BodyGyroMag_std"         
## [69] "time_BodyGyroJerkMag_std"      "freq_BodyAcc_std_X"           
## [71] "freq_BodyAcc_std_Y"            "freq_BodyAcc_std_Z"           
## [73] "freq_BodyAccJerk_std_X"        "freq_BodyAccJerk_std_Y"       
## [75] "freq_BodyAccJerk_std_Z"        "freq_BodyGyro_std_X"          
## [77] "freq_BodyGyro_std_Y"           "freq_BodyGyro_std_Z"          
## [79] "freq_BodyAccMag_std"           "freq_BodyAccJerkMag_std"      
## [81] "freq_BodyGyroMag_std"          "freq_BodyGyroJerkMag_std"
```

## Part 5: creating a second, independent tidy data set with the average of each variable for each activity and each subject

The following table shows the number of observations for each combination of subject and activity.


```r
    table <- table(sub_df$subject, sub_df$descriptive_activity_label)
    table
```

```
##     
##      LAYING SITTING STANDING WALKING WALKING_DOWNSTAIRS WALKING_UPSTAIRS
##   1      50      47       53      95                 49               53
##   2      48      46       54      59                 47               48
##   3      62      52       61      58                 49               59
##   4      54      50       56      60                 45               52
##   5      52      44       56      56                 47               47
##   6      57      55       57      57                 48               51
##   7      52      48       53      57                 47               51
##   8      54      46       54      48                 38               41
##   9      50      50       45      52                 42               49
##   10     58      54       44      53                 38               47
##   11     57      53       47      59                 46               54
##   12     60      51       61      50                 46               52
##   13     62      49       57      57                 47               55
##   14     51      54       60      59                 45               54
##   15     72      59       53      54                 42               48
##   16     70      69       78      51                 47               51
##   17     71      64       78      61                 46               48
##   18     65      57       73      56                 55               58
##   19     83      73       73      52                 39               40
##   20     68      66       73      51                 45               51
##   21     90      85       89      52                 45               47
##   22     72      62       63      46                 36               42
##   23     72      68       68      59                 54               51
##   24     72      68       69      58                 55               59
##   25     73      65       74      74                 58               65
##   26     76      78       74      59                 50               55
##   27     74      70       80      57                 44               51
##   28     80      72       79      54                 46               51
##   29     69      60       65      53                 48               49
##   30     70      62       59      65                 62               65
```

There are anywhere from 36 to 95 observations for each subject and each activity.  The tidy data set is created by taking the average of each measurement variable for each activity and each subject.  The code below shows how the averaging is done for each measurement variable.  It also shows how the tidy data frame is written to a *.txt* file and how the same *.txt* could be read back into R.


```r
    tidy_df <- ddply(
        sub_df, .(subject, descriptive_activity_label), summarize, 
        time_BodyAcc_mean_X = mean(time_BodyAcc_mean_X),
        time_BodyAcc_mean_Y = mean(time_BodyAcc_mean_Y),
        time_BodyAcc_mean_Z = mean(time_BodyAcc_mean_Z),
        time_GravityAcc_mean_X = mean(time_GravityAcc_mean_X),
        time_GravityAcc_mean_Y = mean(time_GravityAcc_mean_Y),
        time_GravityAcc_mean_Z = mean(time_GravityAcc_mean_Z),
        time_BodyAccJerk_mean_X = mean(time_BodyAccJerk_mean_X),
        time_BodyAccJerk_mean_Y = mean(time_BodyAccJerk_mean_Y),
        time_BodyAccJerk_mean_Z = mean(time_BodyAccJerk_mean_Z),
        time_BodyGyro_mean_X = mean(time_BodyGyro_mean_X),
        time_BodyGyro_mean_Y = mean(time_BodyGyro_mean_Y),
        time_BodyGyro_mean_Z = mean(time_BodyGyro_mean_Z),
        time_BodyGyroJerk_mean_X = mean(time_BodyGyroJerk_mean_X),
        time_BodyGyroJerk_mean_Y = mean(time_BodyGyroJerk_mean_Y),
        time_BodyGyroJerk_mean_Z = mean(time_BodyGyroJerk_mean_Z),
        time_BodyAccMag_mean = mean(time_BodyAccMag_mean),
        time_GravityAccMag_mean = mean(time_GravityAccMag_mean),
        time_BodyAccJerkMag_mean = mean(time_BodyAccJerkMag_mean),
        time_BodyGyroMag_mean = mean(time_BodyGyroMag_mean),
        time_BodyGyroJerkMag_mean = mean(time_BodyGyroJerkMag_mean),
        freq_BodyAcc_mean_X = mean(freq_BodyAcc_mean_X),
        freq_BodyAcc_mean_Y = mean(freq_BodyAcc_mean_Y),
        freq_BodyAcc_mean_Z = mean(freq_BodyAcc_mean_Z),
        freq_BodyAcc_meanFreq_X = mean(freq_BodyAcc_meanFreq_X),
        freq_BodyAcc_meanFreq_Y = mean(freq_BodyAcc_meanFreq_Y),
        freq_BodyAcc_meanFreq_Z = mean(freq_BodyAcc_meanFreq_Z),
        freq_BodyAccJerk_mean_X = mean(freq_BodyAccJerk_mean_X),
        freq_BodyAccJerk_mean_Y = mean(freq_BodyAccJerk_mean_Y),
        freq_BodyAccJerk_mean_Z = mean(freq_BodyAccJerk_mean_Z),
        freq_BodyAccJerk_meanFreq_X = mean(freq_BodyAccJerk_meanFreq_X),
        freq_BodyAccJerk_meanFreq_Y = mean(freq_BodyAccJerk_meanFreq_Y),
        freq_BodyAccJerk_meanFreq_Z = mean(freq_BodyAccJerk_meanFreq_Z),
        freq_BodyGyro_mean_X = mean(freq_BodyGyro_mean_X),
        freq_BodyGyro_mean_Y = mean(freq_BodyGyro_mean_Y),
        freq_BodyGyro_mean_Z = mean(freq_BodyGyro_mean_Z),
        freq_BodyGyro_meanFreq_X = mean(freq_BodyGyro_meanFreq_X),
        freq_BodyGyro_meanFreq_Y = mean(freq_BodyGyro_meanFreq_Y),
        freq_BodyGyro_meanFreq_Z = mean(freq_BodyGyro_meanFreq_Z),
        freq_BodyAccMag_mean = mean(freq_BodyAccMag_mean),
        freq_BodyAccMag_meanFreq = mean(freq_BodyAccMag_meanFreq),
        freq_BodyAccJerkMag_mean = mean(freq_BodyAccJerkMag_mean),
        freq_BodyAccJerkMag_meanFreq = mean(freq_BodyAccJerkMag_meanFreq),
        freq_BodyGyroMag_mean = mean(freq_BodyGyroMag_mean),
        freq_BodyGyroMag_meanFreq = mean(freq_BodyGyroMag_meanFreq),
        freq_BodyGyroJerkMag_mean = mean(freq_BodyGyroJerkMag_mean),
        freq_BodyGyroJerkMag_meanFreq = mean(freq_BodyGyroJerkMag_meanFreq),
        time_BodyAcc_std_X = mean(time_BodyAcc_std_X),
        time_BodyAcc_std_Y = mean(time_BodyAcc_std_Y),
        time_BodyAcc_std_Z = mean(time_BodyAcc_std_Z),
        time_GravityAcc_std_X = mean(time_GravityAcc_std_X),
        time_GravityAcc_std_Y = mean(time_GravityAcc_std_Y),
        time_GravityAcc_std_Z = mean(time_GravityAcc_std_Z),
        time_BodyAccJerk_std_X = mean(time_BodyAccJerk_std_X),
        time_BodyAccJerk_std_Y = mean(time_BodyAccJerk_std_Y),
        time_BodyAccJerk_std_Z = mean(time_BodyAccJerk_std_Z),
        time_BodyGyro_std_X = mean(time_BodyGyro_std_X),
        time_BodyGyro_std_Y = mean(time_BodyGyro_std_Y),
        time_BodyGyro_std_Z = mean(time_BodyGyro_std_Z),
        time_BodyGyroJerk_std_X = mean(time_BodyGyroJerk_std_X),
        time_BodyGyroJerk_std_Y = mean(time_BodyGyroJerk_std_Y),
        time_BodyGyroJerk_std_Z = mean(time_BodyGyroJerk_std_Z),
        time_BodyAccMag_std = mean(time_BodyAccMag_std),
        time_GravityAccMag_std = mean(time_GravityAccMag_std),
        time_BodyAccJerkMag_std = mean(time_BodyAccJerkMag_std),
        time_BodyGyroMag_std = mean(time_BodyGyroMag_std),
        time_BodyGyroJerkMag_std = mean(time_BodyGyroJerkMag_std),
        freq_BodyAcc_std_X = mean(freq_BodyAcc_std_X),
        freq_BodyAcc_std_Y = mean(freq_BodyAcc_std_Y),
        freq_BodyAcc_std_Z = mean(freq_BodyAcc_std_Z),
        freq_BodyAccJerk_std_X = mean(freq_BodyAccJerk_std_X),
        freq_BodyAccJerk_std_Y = mean(freq_BodyAccJerk_std_Y),
        freq_BodyAccJerk_std_Z = mean(freq_BodyAccJerk_std_Z),
        freq_BodyGyro_std_X = mean(freq_BodyGyro_std_X),
        freq_BodyGyro_std_Y = mean(freq_BodyGyro_std_Y),
        freq_BodyGyro_std_Z = mean(freq_BodyGyro_std_Z),
        freq_BodyAccMag_std = mean(freq_BodyAccMag_std),
        freq_BodyAccJerkMag_std = mean(freq_BodyAccJerkMag_std),
        freq_BodyGyroMag_std = mean(freq_BodyGyroMag_std),
        freq_BodyGyroJerkMag_std = mean(freq_BodyGyroJerkMag_std)
    )
    
    write.table(tidy_df, "./tidy_data_set.txt")
    read_back_in_to_check <- read.table("./tidy_data_set.txt", header = TRUE)
```

The resulting tidy data set consists of 180 rows (30 subjects * 6 activities). This concludes the assignment writeup.  Thanks for taking the time to read this.
