# Coursera Machine Learning Project

### Load Libraries
```r
library(caret)
library(randomForest)
library(rattle)
```

### Load Data
```r
data <- read.csv("pml-training.csv", na.strings=c("NA","","#DIV/0!"))
```
### Split data into training and test sets
```r
inTrain <- createDataPartition(y=data$classe, p=0.7, list=FALSE)
training <- data[inTrain,]
testing <- data [-inTrain,]
```
### Cleaning data (training set)
```r
trainSubset <- training[,-grep("X|name|timestamp|window", colnames(training), value=FALSE)]
NAcols <- apply(trainSubset, 2, function(x) sum(is.na(x)))/nrow(trainSubset)
trainSubset <- trainSubset[!(NAcols > 0.75)]
```
### Cleaning data (testing set)
```r
testSubset <- testing[,-grep("X|name|timestamp|window", colnames(testing), value=FALSE)]
NAcols <- apply(testSubset, 2, function(x) sum(is.na(x)))/nrow(testSubset)
testSubset <- testSubset[!(NAcols > 0.75)]
```
### Predicting with Trees 
```r
set.seed(1234)
modelFit1 <- train(classe ~., method="rpart", data = trainSubset)
fancyRpartPlot(modelFit1$finalModel)
```
