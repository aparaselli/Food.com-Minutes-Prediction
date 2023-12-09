# Food.com-Minutes-Prediction
By Jennifer Hung & Athulith Paraselli
## Introduction
As college students who are both working and studying at the same time, finding time to cook can be difficult and time-consuming in itself. We wanted to see what variables affect how long it takes for a dish to be made, and perhaps help other students take into consideration when allocating time to prepare a meal. Thus, we ask the question of what predicts the number of minutes per recipe?

## Data Cleaning
Our dataset is scraped from food.com, and we went through the same cleaning process as we did in Project 3 (linked [here](https://github.com/aparaselli/Food.com-Rating-Analysis.git).) 
We cleaned two additional columns:
1. Turned `tags` into lists of strings because it was initially all `str` values,
2. Changed `submitted` into datetime for easier analysis.

## Problem Identification
### Prediction problem
We are trying to **predict minutes it takes to prepare a recipe**, which would be a type of **regression** problem as we want to figure out what number of minutes depends on. 
### Response variable
  Our response variable is the **number of minutes per recipe**, and we chose **R^2** as a metric of evaluation because it measures the proportion of variance. Other potential metrics included Mean Squared Error, but this was prone to outliers and our minutes data ranged from 0 to 1 million minutes. R^2 also reports goodness of fit, so the closer to 1, the better the fit of the model. 
  We used information such as number of steps (`n_steps`), calories (`calories`), tags (`tags` with labels such as `"easy"`, `"for large groups"` etc.), and number of ingredients (`n_ingredients`). These are all information that can be acquired before calculating how long it takes to make a dish. Other columns such as ratings or reviews should be noted after minutes were recorded, so it doesn’t make sense to use them for prediction. 

## Baseline Model
We started off with a very basic model that used 2 features and fitted to a Linear Regression line.
### Features
1. `n_steps` is a quantitative, nominal feature (we are not ranking anything better because it has more steps)
2. `calories` is a continuous quantitative, nominal  feature, as having more calories does not necessarily make it have more minutes to prepare a dish.
### Encoding of features
We mapped `calorie`s by negative square root function because when we plotted it, it appeared to be shaped in either a negative logarithmic or negative square root function. We chose to shape it to negative square root because `minutes` included values of 0, which would result in NaN when passed through log. 
When we plotted `n_steps` against `minutes`, it appeared to be a fairly even distribution, thus in our baseline model we did not make any changes to that column.
### Evaluation of model performance
Our baseline model reported an R^2 value of `0.00015936555775553085`, which means it is **not the best model** as it can only explain less than 0.001% of the variance. The R^2 value is fairly far from 1, which would indicate that it was far from a good fit. It seems like our model has a lot of space for improvement, as we should get a model with R^2 of something closer to 1. 
## Final Model
## Fairness Analysis
