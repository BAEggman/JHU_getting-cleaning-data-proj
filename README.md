#### Getting and cleaning data codebook
---

## Data Source

---
*From UCI HAR datasets. Link : http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones  

---

Dependencies : 
rehape2

--- 
how to run analysis

1. read data from local directories and bind them into single datasets
```
x_train <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/train/X_train.txt"))
y_train <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/train/y_train.txt"))
s_train <- read.table(paste(sep = "",  "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/train/subject_train.txt"))

s_test <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/test/subject_test.txt"))
x_test <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/test/X_test.txt"))
y_test <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/test/y_test.txt"))

x_bind <- rbind(x_test, x_train)
y_bind <- rbind(y_test, y_train)
s_bind <- rbind(s_test, s_train)
```
2. Load feature info(features.txt) & Feature labels to extract
```
ft <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/features.txt"))

ft_label <- read.table(paste(sep = "", "/Users/mac/Desktop/R/Getting and cleaning data/UCI HAR Dataset/activity_labels.txt"))
ft_label[,2] <- as.character(ft_label[,2])
```

3. extract mean, st_deviation features by using grep() function, use descriptive activity names
```
subcol <- grep("-(mean|std).*", as.character(ft[,2]))

subcol_names <- ft[subcol, 2]
subcol_names <- gsub("-mean", "Mean", subcol_names)
subcol_names <- gsub("-std", "Std", subcol_names)
subcol_names <- gsub("[-()]", "", subcol_names)

x_bind <- x_bind[subcol]
udn <- cbind(s_bind, y_bind, x_bind)
colnames(udn) <- c("Subject", "Activity", subcol_names)
```
4. map variables to descriptive names and set them as factors

```
udn$Activity <- factor(udn$Activity, levels = ft_label[,1], labels = ft_label[,2])
udn$Subject <- as.factor(udn$Subject)
```

5. use melt() & dcast functions to make independant tidy data sets
```
meltedData <- melt(udn , id = c("Subject", "Activity"))
tidyData <- dcast(meltedData, Subject + Activity ~ variable, average)
```
6. Use write.table() function to save tidy files to your local directory
```
write.table(tidyData, "./tidy_dataset.txt", row.names = FALSE, quote = FALSE)
```
