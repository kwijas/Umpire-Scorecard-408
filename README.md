# Elastic Net Regression on MLB Umpire Scorecard Data

##Introduction
The data for this project was sourced from [UmpireScorecards](https://www.umpscorecards.com/data/umpires) for the 2015-2022 MLB seasons. 

##Data Cleaning
Some inital steps were conducted before the analysis could be done. 
1. The date variable was removed from the dataset due to formatting issues.
2. Missing data, labeled as "ND," was removed from the dataset.
3. Certain columns containing numerical data were stored as factors, thus, they were converted back to numeric varaibles.
4. After steps 1-3 were performed, the VIF of a model with all terms included was ran and produced an error stating varaibles within the model were multicolinear. Therefore, the varaibles: "... above expected" and total pitches (the sum of correct and incorrect pitches) were removed.   
5.  The original authors explain in depth how their accuracy and expected values are calculated. For accuracy, it is represented by the proportion of correct calls and a “certain subset of taken pitches,” which may come from a game, season, etc. To determine if a pitch was called correctly, the original authors use an algorithm to calculate the likelihood that a pitch was a strike. This involves using a Monte Carlo simulation of a pitch’s true potential location given its reported location, and a distribution of measurement error found in the Hawk-Eye tracking system. Therefore, to determine if a pitch was called incorrectly, the authors state one of two conditions must hold: the probability that a pitch was a strike was over 90%  and it was called a ball, and the probability that a pitch was a ball was over  90%  and it was called a strike. Since these calculations involve simulated data, an inconsistent subset of pitches,  and are not based on the individual game data given in the dataset, the variables: expected correct calls, expected incorrect calls, and expected accuracy were removed.

Going into model selection, the variables being considered are: **umpire, home, away, home team runs, away team runs, incorrect calls, correct calls, accuracy, consistency, favor home, and total run impact.**

##Methodology
Since the goal of this project is prediction, as opposed to inference, a regularization technique is needed. 

**For replication and interpretation purposes in the final model output, a seed was set before regression principles were performed.**

The steps conducted were:
1. The initial variables included were made into a matrix in order to use the glmnet function in R.
2. Cross validation was used to find the optimal alpha value, using the variables mentioned previously as the predictors and accuracy as the response variable with 10 folds.
3. The alpha value found in the previous step was used in Elastic Net regression to find the optimal lambda value that minimizes the mean squared prediction error and adds the standard error estimate in order to choose a more regularized model. The optimal lambda value is around 0 and contains a model with 11 out of the original 13 variables included.
4. The coefficients were extracted from a model using that lambda value and the prediction values were found then plotted.

##Conclusions
