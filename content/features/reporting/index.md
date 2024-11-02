+++
author = "FERMATRICA"
title = "Anaysis & Reporting"
date = "2024-10-16"
description = "Anaysis & Reporting"
tags = [
    "analysis",
    "reporting"
]
series = ["Features Guide"]
+++

![Analysis and reporting](/features/reporting/intro.jpg)

*Work in progress*

## 1. Statistical Analysis

When we build a model, we need some guidelines to understand whether our model is good enough or not. The first part of reporting includes metrics and tests to evaluate the quality of a model.

General metrics of model quality on the training sample (R², RMSE, MAPE) provide information about the goodness of fit. The MAPE on the test sample shows the forecast accuracy.

Statistical properties of a model (for time-series models) help to delve deeper into the analysis (stability of the model and correctness of model specification):

- Tests for omitted variables, autocorrelation, stationarity, and normality of residuals;
- A test for the quality of the forecast on the test sample (test for systematic bias in the forecast);
- Multicollinearity metrics (VIFs).

It is necessary to sum up all the results of tests and metrics to make a decision on the quality of a model. However, we cannot stop here because we model a real-life process, so we should be sure that the structure of the model is relevant to it (signs of coefficients, transformations and impacts of regressors). Therefore, we should proceed to the second part of the reporting.

## 2. Business Analysis


### 2.1. Fit and Predict

## 3. Planning Options


## 4. Split and Budget Optimization


## 5. Slide Generator


## 6. Source code and documenation

### 6.1 Source code

FERMATRICA consists of three separate repositories: one for utilities, one for reporting and one for core functionality (model definition, predicting, optimization). analysis and reporting functionality is located in [FERMATRICA_REP package](https://github.com/FERMATRICA/fermatrica_rep).

### 6.2. Documentation

- [Fermatrica MMM guide](/fermatrica/guides/FERMATRICA_and_MMM_instruction.html)
- [API Reference](/fermatrica_rep/api/index.html)

### 6.3. See also

- [Model and optimization](/features/model)
- [Customization](/features/customize)


