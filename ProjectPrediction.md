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
![alt text](https://github.com/gmatias87/PracticalMachineLearning/blob/master/RplotModelFittTree.png "Logo Title Text 1")

### Predicting with random forest
```r
modelFit2 <- randomForest(classe ~., data=trainSubset, type="class")
print(modelFit2)
```
```
## Call:
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 7
## 
##         OOB estimate of  error rate: 0.58%
## Confusion matrix:
##      A    B    C    D    E class.error
## A 3902    2    0    1    1 0.001024066
## B   17 2635    6    0    0 0.008653123
## C    0   13 2380    3    0 0.006677796
## D    0    0   26 2225    1 0.011989343
## E    0    0    2    7 2516 0.003564356
```

### Predicting new values
```r
testPredictVal1 <- predict(modelFit1, testSubset, type="class")
confusionMatrix(testPredictVal1, testSubset$classe)

testPredictVal2 <- predict(modelFit2, testSubset, type="class")
confusionMatrix(testPredictVal2, testSubset$classe)
```
```
## Confusion Matrix and Statistics
##
##          Reference
## Prediction    A    B    C    D    E
##         A 1674    4    0    0    0
##         B    0 1133    3    0    0
##         C    0    2 1020    5    0
##         D    0    0    3  959    3
##         E    0    0    0    0 1079
##
## Overall Statistics
##                                          
##               Accuracy : 0.9966          
##                 95% CI : (0.9948, 0.9979)
##    No Information Rate : 0.2845          
##    P-Value [Acc > NIR] : < 2.2e-16       
##                                          
##                  Kappa : 0.9957          
## Mcnemar's Test P-Value : NA              
##
## Statistics by Class:
##
##                     Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   0.9947   0.9942   0.9948   0.9972
## Specificity            0.9991   0.9994   0.9986   0.9988   1.0000
## Pos Pred Value         0.9976   0.9974   0.9932   0.9938   1.0000
## Neg Pred Value         1.0000   0.9987   0.9988   0.9990   0.9994
## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
## Detection Rate         0.2845   0.1925   0.1733   0.1630   0.1833
## Detection Prevalence   0.2851   0.1930   0.1745   0.1640   0.1833
```
### Predicting using test data
```r
dataTest <- read.csv("pml-testing.csv")
dataTest <- dataTest [,-grep("X|name|timestamp|window", colnames(testing), value=FALSE)]
NAcols <- apply(dataTest, 2, function(x) sum(is.na(x)))/nrow(dataTest)
dataTest <- dataTest[!(NAcols > 0.75)]
testPredictVal1 <- predict(modelFit1, dataTest, type="class")
dataTest$classe <- predict(modelFit1,dataTest)
```

