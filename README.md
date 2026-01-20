# Elastic Net Regression on MLB Umpire Scorecard Data

## Introduction
The data for this project was sourced from [UmpireScorecards](https://www.umpscorecards.com/data/umpires) for the 2015-2022 MLB seasons. 

## Data Cleaning
Some inital steps were conducted before the analysis could be done. 
1. The date variable was removed from the dataset due to formatting issues.
2. Missing data, labeled as "ND," was removed from the dataset.
3. Certain columns containing numerical data were stored as factors, thus, they were converted back to numeric varaibles.
5. After steps 1-3 were performed, the VIF of a model with all terms included was ran and produced an error stating varaibles within the model were multicolinear. Therefore, the varaibles: "... above expected" and total pitches (the sum of correct and incorrect pitches) were removed.   
6.  The authors explain how their accuracy and expected values are calculated. Since these calculations involve simulated data, an inconsistent subset of pitches,  and are not based on the individual game data given in the dataset, the variables: expected correct calls, expected incorrect calls, and expected accuracy were removed.
7.  Some infuluential observations were removed.

Going into model selection, the variables being considered are: **umpire, home, away, home team runs, away team runs, incorrect calls, correct calls, accuracy, consistency, favor home, and total run impact.**

## Methodology
Since the goal of this project is prediction, as opposed to inference, a regularization technique is needed. 

**For replication and interpretation purposes in the final model output, a seed was set before regression principles were performed.**

The steps conducted were:
1. The initial variables included were made into a matrix in order to use the glmnet function in R.
2. Cross validation was used to find the optimal alpha value, using the variables mentioned previously as the predictors and accuracy as the response variable with 10 folds. This value was 0.9.
   <p align="center">
   <img width="450" height="350" alt="image" src="https://github.com/user-attachments/assets/e6acfca2-844b-41d4-b843-a5c26e16a355" />
</p>
4.  The alpha value found in the previous step was used in Elastic Net regression to find the optimal lambda value that minimizes the mean squared prediction error and adds the standard error estimate in order to choose a more regularized model. The optimal lambda value is around 0 and contains a model with 11 out of the original 13 variables included.

<p align="center">
   <img width="450" height="350" alt="image" src="https://github.com/user-attachments/assets/3302ce06-957c-4776-ba17-6ac1e0f32c90" />
</p>
5. The coefficients were extracted from a model using that lambda value and the prediction values were found then plotted.

## Conclusions

Since the alpha value from cross validation was very close to 1, the Elastic Net Regression was heavily reliant on LASSO rather than Ridge. Thus, there was more variable selection being conducted on the model rather and less reduction of the magnitudes of the coefficients to reduce overfitting. The minimum lambda with the addition of the standard error estimate was around 0.00094. Since the lambda value is very small, this indicates less shrinkage on the coefficients. Therefore, the penalty term does not have that much influence on the beta hat values in this model. 

When extracting the coefficients of this model, both “home” and “favor home” shrunk to 0 by the LASSO portion of Elastic Net, indicating these variables are unimportant in the final model output. The sign of the coefficients is worth noting here. 
- Those with negative signs include: umpire, away, home team runs, away team runs, incorrect calls, total run impact, and the square of correct calls. Thus these variables have a negative effect on predicting the accuracy of an umpire. Some of these variables were expected to be negative, like the number of incorrect calls made in the game and who the umpire was.
  - The coefficient for incorrect calls was -0.74939, indicating as the number of incorrect calls increases by one call, the predicted accuracy of umpires is expected to decrease by 0.74939% when holding all other variables constant.

- The positive signed coefficients are: correct calls, consistency, and the square of incorrect calls and total run impact. Understandably, the number of correct calls made in a game would have a positive impact on predicting accuracy.
  - The coefficient for the number of correct calls was 0.081016, indicating as the number of correct calls increases by one call, the predicted accuracy of umpires is expected to increase by 0.081016% when all other variables are held constant.
  
Predictions were pulled from the optimal Elastic Net model for the accuracy of an umpire per game. 
- The lowest predicted accuracy score for one game using the predict function was 81.6%, where the umpire was Ron Kulpa and the game was the Rangers at the Yankees played on May 22, 2015. The accuracy of this umpire during this game in the original dataset (before prediction) was 79.5%.
- The highest predicted accuracy score for one game was 100.97%, where the umpire was Will Little and the game was the Twins at the Reds played on April 27, 2018. The accuracy before prediction was 98.6% for this umpire. Overall, the accuracy scores were increased for each game.

## Final Thoughts
While the model does a good job creating a working model when accounting for overlaps in umpires and teams, it does a poor job when using the predict function to get the prediction values based on the minimum lambda estimate. The accuracy estimates per game are raised post model building, causing some game umpire accuracy’s to be above 100. When trying to correct this with a family of binomial, rather than gaussian, an error occurs. Thus, in the future it would be wise to consider using logistic Elastic Net regression, rather than linear regression. Other future prospects would be trying to include the date column or add the division of teams and see if these variables are significant. It is important to create a model that predicts the accuracy of MLB umpires well, as Major League Baseball is a high grossing industry that relies on the support of fans across the world. Therefore, hiring home plate umpires that are accurate in pitch calls improves both the viewing and playing experience.  

