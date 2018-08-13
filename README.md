# Practical-Machine-Learning-Assignment
## Background
## Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset).

## Data
##The training data for this project are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv]

## The test data are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv]

## The data for this project come from this source: [http://groupware.les.inf.puc-rio.br/har]. If you use the document you create for this class for any purpose please cite them as they have been very generous in allowing their data to be used for this kind of assignment.
## Approach:
## Our outcome variable is classe, a factor variable. For this data set, “participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in 5 different fashions: - exactly according to the specification (Class A) - throwing the elbows to the front (Class B) - lifting the dumbbell only halfway (Class C) - lowering the dumbbell only halfway (Class D) - throwing the hips to the front (Class E)

## Two models will be tested using decision tree and random forest. The model with the highest accuracy will be chosen as our final model.

## Cross-validation
## Cross-validation will be performed by subsampling our training data set randomly without replacement into 2 subsamples: TrainTrainingSet data (75% of the original Training data set) and TestTrainingSet data (25%). Our models will be fitted on the TrainTrainingSet data set, and tested on the TestTrainingSet data. Once the most accurate model is choosen, it will be tested on the original Testing data set.
```{r}
library(lattice); library(ggplot2); library(caret); library(randomForest); library(rpart); library(rpart.plot)
```
## Read Data
```{r}
trainingset <- read.csv("pml-training.csv", na.strings=c("NA","#DIV/0!", ""))
testingset <- read.csv("pml-testing.csv", na.strings=c("NA","#DIV/0!", ""))
```
## Delete column with missing values
```{r}
trainingset <- trainingset[,colSums(is.na(trainingset)) == 0]
testingset <- testingset[,colSums(is.na(testingset)) == 0]
```
## Delete irrelevant first 7 columns
```{r}
trainingset <- trainingset[,-c(1:7)]
testingset <- testingset[,-c(1:7)]
```
## Train dataset
```{r}
traintrainset <- createDataPartition(y=trainingset$classe, p=0.75, list=FALSE)
TrainTrainingSet <- trainingset[traintrainset, ] 
TestTrainingSet <- trainingset[-traintrainset, ]
plot(TrainTrainingSet$classe, col="blue", main="Plot of  variable classe of TrainTrainingSet data set", xlab="classe", ylab="Frequency")
```
## Fit Discrete Tree Model
```{r}
modelfit1 <- rpart(classe ~ ., data=TrainTrainingSet, method="class")
predict1 <- predict(model1, TestTrainingSet, type = "class")
rpart.plot(modelfit1, main="Classification Tree", extra=102, under=TRUE, faclen=0)
```
## Confusion Matrix
```{r}
confusionMatrix(predict1, TestTrainingSet$classe)
```
## Fit Random Forest Model
```{r}
modelfit2 <- randomForest(classe ~. , data=TrainTrainingSet, method="class")
predict2 <- predict(modelfit2, TestTrainingSet, type = "class")
confusionMatrix(prediction2, TestTrainingSet$classe)
```
## Decision on which Prediction Model to Use:
## Random Forest algorithm performed better than Decision Trees. Accuracy for Random Forest model was 0.995 (95% CI: (0.993, 0.997)) compared to Decision Tree model with 0.739 (95% CI: (0.727, 0.752)). The Random Forests model is choosen. The expected out-of-sample error is estimated at 0.005, or 0.5%.

## Submission
## Here is the final outcome based on the Prediction Model 2 (Random Forest) applied against the Testing dataset
