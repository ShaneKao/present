Practical Machine Learning Prediction Assignment Writeup
========================================================
author: Shane Kao
date: 2014/06/15

Background
========================================================

The goal of your project is to predict the manner in which they did the exercise. This is the `classe` variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.


Variables
========================================================

The following variables to predict with the model.

```
 [1] "gyros_belt_x"      "gyros_belt_y"      "gyros_belt_z"     
 [4] "accel_belt_x"      "accel_belt_y"      "accel_belt_z"     
 [7] "magnet_belt_x"     "magnet_belt_y"     "magnet_belt_z"    
[10] "gyros_arm_x"       "gyros_arm_y"       "gyros_arm_z"      
[13] "accel_arm_x"       "accel_arm_y"       "accel_arm_z"      
[16] "magnet_arm_x"      "magnet_arm_y"      "magnet_arm_z"     
[19] "gyros_dumbbell_x"  "gyros_dumbbell_y"  "gyros_dumbbell_z" 
[22] "accel_dumbbell_x"  "accel_dumbbell_y"  "accel_dumbbell_z" 
[25] "magnet_dumbbell_x" "magnet_dumbbell_y" "magnet_dumbbell_z"
[28] "gyros_forearm_x"   "gyros_forearm_y"   "gyros_forearm_z"  
[31] "accel_forearm_x"   "accel_forearm_y"   "accel_forearm_z"  
[34] "magnet_forearm_x"  "magnet_forearm_y"  "magnet_forearm_z" 
```


Machine Learning Algorithm
========================================================

Training Set: 70%
Test Set:30 %

```r
ind=sample(2,nrow(tidy.training),
           replace=TRUE,prob=c(0.7,0.3))
trainData=tidy.training[ind==1,]
testData=tidy.training[ind==2,]
```

Use `trainData` to fit Conditional Inference Trees 


```r
library(party)
mytree=ctree(classe~.,data=trainData)
```


In Sample Error
========================================================


```r
(is=table(predict(mytree),
          trainData$classe))
```

```
   
       A    B    C    D    E
  A 3681  152   56   74   19
  B   88 2315  109   76  115
  C   44   81 2166  130   58
  D   91   39   41 1920   49
  E   26   83   50   80 2346
```

```r
(InSampleError=1-sum(diag(is))/sum(is))
```

```
[1] 0.1052
```


Out of Sample Error
========================================================


```r
(os=table(predict(mytree,testData),
          testData$classe))
```

```
   
       A    B    C    D    E
  A 1503   62   44   46   15
  B   65  883   65   35   63
  C   19   72  815   60   29
  D   54   37   36  760   42
  E    9   73   40   35  871
```

```r
(OutofSampleError=1-sum(diag(os))/sum(os))
```

```
[1] 0.1572
```


Prediction
========================================================


```r
f=function(x)
{class(testData[,x])}
c=apply(cbind(1:37),1,f)
tidy.testing=testing[,index]
tidy.testing$classe=
        (tidy.training$classe)[1:20]
write.table(tidy.testing,"tidy.testing.txt",
            sep=",",row.names=FALSE)
tidy.testing=read.table("tidy.testing.txt",
                        sep=",",header=TRUE,colClasses=c)
predict(mytree,tidy.testing)
```

```
 [1] B A C A A E D B A A B C B A E E A B B B
Levels: A B C D E
```

