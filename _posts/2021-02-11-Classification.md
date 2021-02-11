---
layout: post
title: Predicting Response to a Bank Telemarketing Campaign
---

<p>My third project at Metis used supervised machine learning algorithms to predict if a potential bank client would sign up for a term deposit account using [this UCI dataset](https://archive.ics.uci.edu/ml/datasets/Bank+Marketing). By predicting which clients are more likely to sign-up and focusing efforts on those clients there is the potential to increase campaign success rates. </p>

## Models compared:

1. Logistic regression

2. Bernoulli Naive Bayes

3. Random Forest

4. Balanced Random Forest

5. XGBoost

6. Catboost

## Data exploration and cleaning:

<p>I first created a postgresql database to complete some initial data exploration such as looking at the point at which further contact during the telemarketing campaign ceased to yield any term deposit sign-ups and which professions had the highest amount of sign-ups. I checked for null values and droped duplicate rows as well as began some feature engineering. UCI states the observations are ordered by date from May 2008-November 2010 so I was able to add in the year for each observation. I also created new features by binning existing features such as turning individual ages into age groups. As part of my data exploration I also graphed mulitple features looking at the imbalance between the number of people who did and did not sign up for a term deposit.</p>

## Dealing with Class Imbalance:

1. Stratified test/train split

2. Balancing Class Weights

3. SMOTE, Random oversampling, Random undersampling

<p>Since there was slightly less than 8 times as many people that declined signing up for a term deposit than those who did the test/train splot was stratified for each model. While I primarily used balanced class weights as a method to combat class imbalance I also tested SMOTE, random oversampling, and random undersampling with several of the models.</p>

## CatBoost Classifier:
<p>After tuning hyperparameters and testing different methods to combat class imabalance with the various models the CatBoost Classifier preformed the best. While Catboost was not a model covered by Metis I became interested in it as a potential model for this project as it natively handles categorical features without one hot encoding preprocessing.</p> 
<p>I used ROC AUC score, the model's confusion matrix, and f1 macro score as my metrics to gauge performance. While the CatBoost had the highest ROC AUC score, its f1 macro and accuracy scores were very similiar or even lower than some of the other models; however it had a higher percentage of true positive results while maintaining a lower rate of false positives than other models.</p>
![ROC Curve and ROC AUC Score Comparison](/Images/ROC_Curves.svg)
![Catboost Confusion Matrix](/Images/catboost_confusion_matrix.svg)
<p>I used a correlation heatmap to infer the directionality of feature importance for this model.</p>
![Correlation to signing up for a deposit](/Images/deposit_correlation.svg)
![Catboost Feature Importances](/Images/catboost_feature_importance_modified.svg)
