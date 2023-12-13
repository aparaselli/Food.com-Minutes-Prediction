# Food.com-Minutes-Prediction
By Jennifer Hung & Athulith Paraselli
## Introduction
As college students who are both working and studying at the same time, finding time to cook can be difficult and time-consuming in itself. We wanted to see what variables affect how long it takes for a dish to be made, and perhaps help other students take into consideration when allocating time to prepare a meal. Thus, we ask the question of what predicts the number of minutes per recipe?

## Data Cleaning
Our dataset is scraped from food.com, and we went through the same cleaning process and exploratory data analysis as we did in Project 3 ([Click here to read!](https://github.com/aparaselli/Food.com-Rating-Analysis.git)) 
We cleaned two additional columns:
1. Turned `tags` into lists of strings because it was initially all `str` values,
2. Changed `submitted` into datetime for easier analysis.

## Problem Identification
### Prediction Problem
We are trying to **predict minutes it takes to prepare a recipe**, which would be a type of **regression** problem as we want to figure out what variables do number of minutes depend on. 
### Response variable
  Our response variable is the **number of minutes per recipe**, and we chose **$R^2$** as a metric of evaluation because it measures the proportion of variance. Other potential metrics included Mean Squared Error, but this was prone to outliers and our minutes data ranged from 0 to 1 million minutes. $R^2$ also reports goodness of fit, so the closer to 1, the better the fit of the model. 
  
  We used information such as number of steps (`n_steps`), calories (`calories`), tags (`tags` with labels such as `"easy"`, `"for large groups"` etc.), and number of ingredients (`n_ingredients`). These are all information that can be acquired before calculating how long it takes to make a dish as you need the ingredients and steps for a recipe thought out before making it. Other columns such as ratings or reviews should be noted after minutes were recorded, so it doesn’t make sense to use them for prediction. 

## Baseline Model
We started off with a very basic model that used 2 features and fitted to a **Linear Regression** model.
### Features
1. `n_steps` is a **quantitative, nominal** feature (we are not ranking anything better because it has more steps)
2. `calories` is a continuous **quantitative, nominal** feature, as having more calories does not necessarily make it have more minutes to prepare a dish.
### Encoding Of Features
We mapped `calories` by negative square root function because when we plotted it, it appeared to be shaped in either a negative logarithmic or negative square root function. We chose to shape it to negative square root because `minutes` included values of 0, which would result in NaN when passed through log. 
When we plotted `n_steps` against `minutes`, it appeared to be a fairly even distribution, thus in our baseline model we did not make any changes to that column.
### Evaluation Of Model Performance
Our baseline model reported an $R^2$ value of `0.0007803710374110207`, which means it is **not the best model** as it can only explain less than 0.001% of the variance. The $R^2$ value is fairly far from 1, which would indicate that it was far from a good fit. It seems like our model has a lot of space for improvement, as we should get a model with $R^2$ of something closer to 1. 
## Final Model
Our final model had a total of 4 features and was fitted to a **Decision Tree Regressor** model.
### Added Features
1. Standard scaling of `n_steps` and `n_ingredients` (both **quantitative, nominal** features) because we wanted to ensure the evenness of distribution of values through standardization. 
2. For `calories` (continuous **quantitative, nominal** feature) we quantile transformed the data as we knew that calories had extreme outliers (ranging from 0 to ~45k).
Thus, we wanted to use QuantileTransformer to reduce the impact of outliers.
3. Performed One-Hot Encoding with the `tags` column (all **qualitative, categorical** values) through CountVectorizer. We wanted to account for the meaningfulness of certain tags (ex: we predicted that tags that have the word `"easy"` may result in shorter cooking time, while tags like `“for larger groups”` may be longer because it prepares more food.) CountVectorizer performs a bag of words encoding, which returns something similar to One-Hot Encoding for our list of words.

After analyzing the 568 unique tags we ended using the following 8 tags: `for-large-groups`, `for-large-groups-holiday-event`, `for-1-or-2`, `desserts-easy`, `easy`, `no-cook`, `snack`, `snacks-kid-friendly`. If the recipe had the tag, it was labeled 1 for that tag; if not, it was labeled 0.

### Modeling Algorithm
#### Model Selection
We tried two modeling algorithms: Linear Regression and Decision Tree Regressor. We selected Decision Tree Regressor for its higher $R^2$ value when ran with the same features (test trial on max-depth of 3).
#### Hyperparameter Selection
For our hyperparamter selection, we manually ran the training and test set data iteratively to find the best max depth and criterion. We identified the best max-depth as **5** at the cutoff of the graph below. Our criterion was **friedman_mse** as it perfomed a bit better on average (as seen in figure below).

|   Max_Depth |   squared_error |   friedman_mse |\n|------------:|----------------:|---------------:|\n|           1 |      -0.0209999 |     -0.0209999 |\n|           2 |      -0.0752337 |     -0.0752337 |\n|           3 |      -0.235588  |     -0.235588  |\n|           4 |      -2.70197   |     -2.70197   |\n|           5 |       0.589808  |      0.589811  |\n|           6 |       0.588609  |      0.588609  |\n|           7 |       0.576559  |      0.576562  |\n|           8 |       0.605176  |      0.606451  |\n|           9 |       0.593303  |      0.591631  |\n|          10 |       0.517932  |      0.524379  |

### Evaluation Of Model Performance
Our Decision Tree Regressor with a max-depth of 5 had an $R^2$ of `0.07029746244938351`. This value is still far from a good fit of 1, which indicates our Final Model still has room for improvement.
#### Comparison to Baseline Model
In comparison to our Baseline Model's $R^2$ of `0.0007803710374110207`, the Final Model's $R^2$ value is significantly larger (by 100 times). These indicates that our Final Model improved greatly from our Baseline as we tried fitting more features (reducing variance) and transforming them so that they reflect better in the model.
## Fairness Analysis
For our Fairness Analysis, we checked to see if there was a difference in model performance in two groups:

**Group X:** Recipes created **before and including** the year 2014.

**Group Y:** Recipes created **after and excluding** the year 2014.
For our evaluation metric, we used $RMSE$ to evaluate both Group X and Y's model performance. We used a permutation test to see if there is any difference in $RMSE$ for recipes made before 2014 and after 2014 as we do not know the original population.

**Null Hypothesis:** Our model is fair, its $RMSE$ for recipes made before and after 2014 are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:** Our model is unfair. There is a difference between the $RMSE$ made before and after 2014.

The test statistic we selected was the **absolute difference of $RMSE$** because our alternative hypothesis is a two-tailed test on quantitative data.
The observed difference in $RMSE$ is: ``

From our graph above, we see that our p-value equates to ``, which means we **fail to reject the null** at the significance level of 0.05.

We can tentatively conclude that there is no difference in model performance between recipes created before and after 2014. However, we cannot 100% prove that it is completely fair across both groups.

