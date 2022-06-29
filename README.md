# Residential Energy Applicance Classification

* **Author Names**: Dahye Kim, Armin Berger
* **Last Edited**: 30 May 2021 

## Introduction
This program deploys a total of 12 classification models, which seek to predict the usage of residential appliances. More specifically, for the five household appliances - **air conditioner (AC), oven, washer, dryer, and electric vehicle charger (EV)** - we created two to three classification models per appliance. Each of the models perform a binary classification of whether an appliance is on (1) or off (0). All the models were trained on the same data set of 417,720 observations, where each timestamp is equal to 1 minute of household appliance usage. To systematically describe and analyse the full model building process, we have broken down this report into two main sections EDA/data pre-processing, and model development & performance assessment.

| Feature | Description |
| --- | ----------- |
| `X` | Timestamp |
|`load` | electrical load|
|`hourofday` | hours of the day from 0 to 24 |
|`dayofweek` | days of the week (Monday to Sunday) |
|`dif` | difference between load data points |
|`absdif` | absolute value of dif|
|`max`| maximum value of load|
|`var`| variance of load level over 30 min|
|`entropy`| forecastability of time series data|
|`hurst`| long-term memory of a time series data|
|`nonlinear` | nonlinearity coefficient used in Terasvirta`s nonlinearity test|

## Model Development Process
When selecting and developing models, the properties of the data that these models will be trained and tested on needs to be considered. In our case, three noticeable issues in the train and test data became apparent during the EDA process. Firstly, both the training and testing data has a significant class imbalance, with 96-99 % of the observations belonging to class 0. Only the target AC has a better class distribution with a 75/25 split. Secondly, despite that load is a timeseries variable, the extracted variables from 30-min time windows enables us to treat each observation as independent from another. However, when inspecting the test dataset, we realised that some observations are not chronologically ordered. This could imply some errors with the extracted features from ‘load’ variable, and thus our prediction with these erroneous observations could lead to faulty prediction. Additionally, the extracted timeseries features of the first 30 entries of the test set are 0. Since the timeseries features from the first windows are not extracted, these values should be regarded as missing values, and the prediction with these observations could still be erroneous despite imputation. Therefore, the models we deploy must also be robust to missing values. As a result, the models that we are majorly using for predicting all targets are KNN, Linear Discriminant Analysis, and various tree-based models, such as decision tree, random forest, and gradient boosting.  
  
  
For feature selection, the possible methods we can use to derive the significant predictors are regularisation (L1 & L2), stepwise regression using various information criterion, or cross validation of each feature combination from the test set. Nevertheless, the logistic regression model is not a candidate for our predictive task since it is vulnerable to outliers and class imbalance. Therefore, the stepwise regression or regularisation with lasso or ridge cannot be applied for feature selection. Additionally, due to the size of the training set, the cross validation of each combination of all features for all models could be computationally costly. Therefore, the feature selection is performed majorly through the results from exploratory data analysis.  
  
  
The process of model development is as follows:
1. Select relevant features for the used target
2. Perform feature engineering to selected features
3. Split data into a train and validation set after randomly shuffling it (the split ratio depends on the severity of class
imbalance of the data)
4. Select models to use for the target & tune its hyperparameters using 3-fold cross validation
5. Use the optimal hyperparameters to train the models on the train set and evaluate the trained model using the
 validation set
6. Select the best model using 3- or 5-fold cross validation to the entire dataset

## Model Descriptions
This program deployed a total of 14 models to predict five different binary targets – ac, oven, dryer, wash, and ev, each target indicating whether the respective appliance was in use in a 1-minute window. To create the best binary classifier for each target, we first started off by performing EDA, where we observed the statistical characteristics and relationships of each feature to other features, as well as to the target. Then we also inspected the balance and standalone ratio of each target and concluded that non-parametric models are the optimal solutions to all targets due to severe class imbalance of all targets, as well as their very low standalone ratios. For each target we selected features for each target using EDA. The contribution of each feature to each target is evaluated using the results from decision tree models. All models used fine- tuned hyperparameters evaluated by 3-fold cross validation, and the performance of each model was evaluated using the validation-set approach. For those targets with poor performance with original features, such as wash and dryer, new timeseries features with window size of 60-min were extracted and fitted to the models. With each trained models, the test F-1 score is estimated using the 5-folds cross validation, and the model with the highest validation F-1 scores was selected as the final model. In summary, the models selected for each target were:  
  
  
 - AC: Decision Tree, LDA
- Oven: Decision Tree, Random Forest, Ada Boost Classifier 
- Dryer: Decision Tree, Random Forest, Gradient Boosting Classifier
- Wash: Decision Tree, Random Forest, Gradient Boosting Classifier
- EV: Decision Tree, Random Forest, KNN Classifier
