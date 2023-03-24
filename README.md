# Model Building For Recipes And Ratings Predictions
_By Oren Kaplan and Kavya Sriram_ <br>

_Our exploratory data analysis on this dataset can be found <a href="https://sahithucsd25.github.io/Recipe-Rating-Analysis/">here</a>_

_Data Cleaned by Kavya Sriram and Sahith Cherumandanda_


## How Can We Predict The Rating of a Recipe?
_These models were built on data from Food.com_ <br>

The goal of this project was to predict the Rating of recipes using data such as nutrition value, recipe length, and more. Our model involved ordinal regression / multiclass classification with the response value, 'rating', being an ordinal discrete value from 1 - 5. <br>

As for our metric, since this problem involved multiclass classification we found we were unable to use many common metrics such as f1 score and accuracy as they are intended for use with binary classification. To combat this, we chose to implement these metrics by evaluating each indiviual class as its own binary classification problem using sklearns metrics classification report. This allowed us to use important metrics such as f1 score on our heavily imbalanced data set where ratings of 5 were far over-represented. We chose to use accuracy as our metric for our fairness analysis due to its ability to summarize classification data performance in one number.

At the time of prediction, all information should be available to our model because all data is input at the same time to the recipe page.


## Baseline Model
For our baseline model, we decided to model with a decision tree due to its natural ability to perform classification. <br>

For our features we chose to use 3 quantitative features: 'minutes' (how many minutes the recipe takes), 'n_ingredients' (how many ingredients the recipe takes), 'n_steps' (how many steps the recipe takes). <br>
We chose these methods for our baseline model because we intuitively felt that these were strong features and they also required no encoding. We wanted to see how these baseline features performed in our baseline model before later using cross validation and other methods to produce a better final model.<br>

### Performance
The baseline model performance scored at 0.696 when ran on the test data

Overall this initial model performed poorly and had clear issues with overfitting alongside issues with bias towards predicting ratings of 5. This is likely due to the largely imbalanced data set with many more 5s than any other ratings.



## Final Model
For our final model we chose to convert to a random forest classifier instead of a decision tree classifier to help combat the overfitting seen in the baseline model.


### Features
For our final model, we implemented 4 new features: sugar, protein, sodium, and calories <br>

We also removed the n_ingredients feature as we found that it reduced our test set accuracy through feature testing and visualization.<br>

We chose to add nutrition details as a feature because we thought it was one of the highest value features available to us within the data set that we hadn't already used in our baseline model. Through intuition its reasonable that elements of nutrition like sodium content and sugar content could sway ratings as these are things we as humans generally find good tasting. The nutrion data consisted of sugar, fat, saturated fat, carbs, sodium, protein, and calories. From these, we chose to use sugar, sodium, protein, and calories as they seemed the most influential over rating behavior compared to values not percievable like saturated fat.

### Hyperparameters
We implemented GridSearchCV with 10 folds to optimize our hyperparameters, choosing to use GridSearchCV on the hyperparameters: max_depth, max_features, and n_estimators. We chose max_depth and max_features because they should be the main parameters that determine overfitting, and part of our goal with this final model was to improve upon the overfitting seen in our baseline model. We chose n_estimators as a general hyperparam to optimize the speed and performance of our model because after a certain point adding more estimator decision trees offers no benefit to accuracy while taking much longer to run.<br>

The resulting optimal hyperparameters: {'max_depth': 110, 'max_features': 3, 'n_estimators': 300} <br>

### Performance
This model was 12% more accurate compared to our baseline model with a prediction accuracy of 0.837 on testing data. <br>

The final model still has clear issues with bias towards predicting a rating of 5 but overall performs much better on unseen data than our previous decision tree baseline model.

In order to further improve on this model we would have to implement data balancing measures because the support for ratings of 5 is higher than the support for the rest of the ratings combined.

Training Data Performance Confusion Matrix:
<iframe src="assets/training_confusion_matrix.html" width=600 height=400 frameBorder=0></iframe>

Test Data Performance Confusion Matrix
<iframe src="assets/testing_confusion_matrix.html" width=600 height=400 frameBorder=0></iframe>

## Fairness Analysis
For our fairness analysis, we wanted to determine whether our classifier performed fairly between long (>35mins) and short (<=35mins) recipes. <br>

In order to use the accuracy score on our imbalanced data set, we chose to use the one vs rest evaluation which allows us to split our multiclass classifcation problem into many binary classifications which can use accuracy score as a metric. <br>

NULL HYPOTHESIS: Our model is fair. Its precision for long (>35mins) and short(<=35mins) recipes is roughly the same, and any differences are due to random chance. <br>

ALTERNATIVE HYPOTHESIS: Our model is unfair. Its precision for long and short recipes is significantly different <br>

Significance Level: 5% <br>

<iframe src="assets/permfig.html" width=600 height=400 frameBorder=0></iframe>

P Value: 0.00 (very close to 0)<br>

Conclusion: We found that it is likely that our model is not fair between long and short recipes. We can reject the null hypothesis that our model is fair, its precision for long (>35mins) and short(<35mins) recipes is roughly the same, and any differences are due to random chance.