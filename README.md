# Predicting-Recipie-Ratings

## Framing the Problem

### My Prediction Problem

Can we predict the rating of a review on a recipe based on different characteristics of the review. 

This is a classification problem, with multi-classes. The reponse variable will be the rating of the review from 0-5 inclusive. This reponse variable was chosen because the accuracy of my model will gage if the rating given by the prediction is accurate to the overall true ratings for the recipe, as there may be several reviews for a single rating giving an average, even though we are not using the average rating. 

I will be using accuracy as the metric for my model. This will provide the proportion of correct guesses for my model. I chose this metric as I believe all the classes have an equal weight of importance.

Our date provides us with many columns describing each recipe. However, the information which will be available at the time of the prediction include: cooking time in minutes, number of steps, number of ingredients, number of tags, and the nutrition values. These will be the columns used to train the model.

Also, the original data has extremely unbalenced class proportion. Ratings of 5 were around 70% of all reviews. If I were to train this data, my model would most likely just predict all reviews to be 5, as this would be sure to give an accuracy of 70%. This didn't feel to good, so I randomly sampled the data so that each class would be represented equally in the training data. This sampled data is what I used to train my models.


## Baseline Model

Initially I tried linear regression with average rating, however, most of the ratings only had 1 review. Which meant that the average rating would never provide the actual correct rating as it would likely return a float value instead of an int. I was not very satisfied with this so I moved to classification with a Decision Tree. 

The features in my baseline model included minutes and number of steps. 

The minutes column was quantative discrete so I did not have to perform any encodings. The minutes column was binarized so that all recipes which took over 66 minutes turned to a value of 1, and all below was 0. I did this because during my EDA, I noticed most ratings that weren't 4 or 5 had much larger cooking times.

The number of steps was also quantative discrete. The number of steps was power transformed quadratically as during my EDA, I noticed the more number of steps, the more likely the rating would be lower. The power transformation would help distinguish outliers so that my model could more clearly see recipes with too many steps.

My decision tree used a max recursion depth of 3 as my original thought process was that since I had 5 classes I would only need close to 5 leaf nodes.

#### Baseline Model Performance:
Accuracy: 0.1039

This model performed pretty poorly however. I believe there is a more useful way to transform our minutes and steps columns. Also, the decision tree will definitely need a higher max depth as it was hard for my model to accuratly find higher rated recipes, especially because minutes was binarized so there was a lot of information lost.


## Final Model

Added/Changed Features:

* Minutes

The minutes transformation was changed to a standard scaler transformation. This meant that outliers would still be weighted heavily but now in both directions. Recipes with average cooking time would keep some information and help with determining how to classify higher rated recipes more specifically than the binarizer.

* Number of Steps

This column had its transformation taken away and the orignal column values were used. I had originally squared these values to highlight outliers, however, the variance for minutes within each rating group still varried by quite a large margin, so keeping the column as is helped keep average cooking time together and outliers still stood out.

* Calories

The calorie scatterplot showed that many lower ratings had higher calorie counts. However, the cutoff of calories for good ratings was too harsh. Thus we log transformed the calorie data so that our cutoff included higher calories but still kept calories above a certain margin a lower rating.

* Number of Ingredients

When we checked the number of ingredients means by ratings groups, it was very similar to the minutes column. There seemed to be a sweet spot where outliers of a certain range had lower ratings overall. Thus we used a standard scaler transformation to lesson the impact of ingredient cuonts closer to the mean but still include the cut offs for outliers.


Model and Hyperparameters:

I ended up using the decision tree classifier. This allowed my to make multi-class predictions and keep the ratings from 0-5 based on my feature columns. In order to find the right hyperparameters for my decision tree I used a grid search. In this grid search I tested my model with different max depths from 0-10 with a cross validation of 5 folds. The max depth which provided the best valid accuracy was 6.

#### Final Model Performance:
Accuracy: 0.3789

My Final Model is an improvement to my baseline model by about double the accuracy, however it still isn't near where I would like it to be. I believe diving into the tags column could be more helpful in finding what spacifically is a tell for a rating of a recipe.


## Fairness Analysis

Fairness Analysis Question:
Does my model perform worse for recipes which take more than 30 minutes to make?

Groups:
* Recipe reviews where the recipe is equal to or under 30 minutes
* Recipe reviews where the recipe is over 30 minutes.

Evaluation Metric:
* Accuracy

Null Hypothesis:
* The diffference in correctness for recipes under 30 minutes and over 30 minutes is due to random chance.

Alternative Hypothesis:
* The classifier's accuracy is higher for recipes under 30 minutes.

Test Statistic:
* Difference in accuracy (under 30 min accuracy - over 30 min accuracy)

Significance level:
* 0.01

Observed Value:
* 0.1940

Resulting P-Value:
* 0.00

Conclusion:
The observed difference in accuracy between recipes above 30 minutes and under 30 minutes may not be do to random chance. In my 1000 simulations, I did not find a single instance where we got an accuracy differnce equal to or greater than 0.1940. We accept our alternative hypeothesis with a p-value of 0.00.







