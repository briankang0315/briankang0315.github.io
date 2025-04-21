---
title: "Fundamentals of Data Analytics (32130) Week 8"
date: 2025-04-09 210000 +1100
categories: [ Masters, Data Analytics ]
tags: [ 석사, Masters, Data Analytics, "32130" ]
author: <author_id>   
---

## Classifier

its job is to tell you the probability of how accurate it is.

classifier will create a line to separate all the values,
instead of scatter plot, transfer the representation of indiv value to distribution representation
= ROC curve

## Confusion Matrix

### Type I error FP
falsely predicted as positive, obvious errors

### Type II error FN
was actually positive but were predicted negative

## Measures of performance 

### Accuracy rate
the ratio of correct predicitions to all predictions.

Number of true positives and true negatives / total number of predictions

![Screenshot 2025-04-09 at 2.22.19 PM.png](../assets/img/screenshots/32130/wk8/Screenshot%202025-04-09%20at%202.22.19%E2%80%AFPM.png)

### Error rate

the ratio of incorrect predictions to all predictions,
(1 - accuracy). Number of false positives and false negatives divided by the total number of predictions.

![Screenshot 2025-04-09 at 2.28.43 PM.png](../assets/img/screenshots/32130/wk8/Screenshot%202025-04-09%20at%202.28.43%E2%80%AFPM.png)

## ROC curve
Receiver Operating Characteristic (ROC) curve

uses accuracy and error rate

True Positive Rate vs False Positive Rate

straight diagonal line: the results are the same, can't distinguish between positive and negative = Random Classifier

50% chance of getting the right answer - very bad model. 

## AUC 

Area under the ROC curve

probability that the model will rank the positive higher than the negative

the accuracy

## KNN

doesn't need to specify learner and predictor node

*The only one*
