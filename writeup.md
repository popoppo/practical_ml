Practical Machine Learning - Course Project : Writeup
========================================================

What method did I choose and Why?
----------------------------------
From the statement of the Course Project, I thought the accuracy of prediction was more important than the calculation speed. Therefore, I chose the "random forest" as the training method.

About cross validation
----------------------------------
As [this link](http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm#ooberr) says,
random forests doesn't need cross-validation, so we don't have to care about it.

Building model and sample error evaluation
--------------------------------------------
Whole training data was read from pml-training.csv into pml_train,
and I omitted some columns which had NA, non numerical data, and timestamps.
Then pml_train was
```
> dim(pml_train)
[1] 19622    54
> names(pml_train)
 [1] "num_window"           "roll_belt"            "pitch_belt"           "yaw_belt"            
(snip)    
[53] "magnet_forearm_z"     "classe"
```

I started at creating a small training data set with 10% of pml_train for simplification.

```
> trIndex <- createDataPartition(y = pml_train$classe, p=0.1, list=F)
> trSet <- pml_train[trIndex, ]
```

Using train() with method="rf", I created a model.

```
> modFit <- train(trSet$classe ~., data=pml_train, method="rf")
> modFit
Random Forest 

1964 samples
 53 predictors
  5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Bootstrapped (25 reps) 

Summary of sample sizes: 1964, 1964, 1964, 1964, 1964, 1964, ... 

Resampling results across tuning parameters:

 mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
 2     0.922     0.901  0.0124       0.0156  
 27    0.948     0.935  0.0101       0.0128  
 53    0.938     0.922  0.0105       0.0132  

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 
```

If I used 0.948 for 20 predictions, the probability of having no erros was

> 0.948**20 = 0.343

It looked not good. Then I used 30% of pml_train, and I got

```
> modFit <- train(trSet$classe ~., data=pml_train, method="rf")
> modFit
Random Forest 

5889 samples
 53 predictors
  5 classes: 'A', 'B', 'C', 'D', 'E' 
(snip)
 mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
 2     0.977     0.97   0.00381      0.00482 
 27    0.985     0.981  0.00246      0.00311 
 53    0.978     0.973  0.00574      0.00724 
```

Here the probability of the full mark was

> 0.985**20 = 0.739

It was better than the last one, but enough to go ahead?

According to the statement for submission, we could submit an answer for each problem up to 2 times.
Considering that, I used the last model for 20 predictions.
