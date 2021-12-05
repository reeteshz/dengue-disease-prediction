# Group-16 for DSBA 6162 KDD Project
## Members - 
- William (Billy) Edwards 
- Samihan Jawalkar
- Shubham Shah
- Reetesh Zope


## Introduction
Dengue is a viral disease that is transmitted by mosquitoes in tropical areas that affects millions of people each year of all nationalities. It is estimated that there are close to 390 million infections of the disease each year – including about 500,000 severe cases that mostly affect children. It is said that severe cases can cause extreme bleeding, low blood pressure, and eventually lead to death. Dengue is now considered endemic in over 100 countries and several US territories. The disease was once only located in Southeast Asia and the Pacific Islands. It is now spreading to parts of Latin America. More troubling for the US, in recent years, dengue outbreaks have hit several states where the Aedes vector mosquitoes are endemic.

The hope of this study aims to see if the spread of the disease is related to climate variables such as temperature, precipitation and humidity, and to see if steps can be taken to reduce the impact of Dengue and reduce future epidemics. Data has been collected from multiple sources to perform this study such as: US Federal Government agencies (like the Centers for Disease Control and Prevention and the National Oceanic and Atmospheric Administration), US Department of Commerce, and US universities.

## Domain Exploration and Resources
https://www.sciencedirect.com/science/article/pii/S0140673614605729?casa_token=4KzqW99aHm0AAAAA:Fd3Pk9W4rZLvaddr2m5bZwAUiLB_ck78mNn7PL31etzz8I3yb0YycbXVh8aiqnTm07OGofVt2fo
https://www.nejm.org/doi/full/10.1056/nejmra1110265

## Data Resources
https://www.drivendata.org/competitions/44/dengai-predicting-disease-spread/page/81/
https://obamawhitehouse.archives.gov/blog/2015/06/05/back-future-using-historical-dengue-data-predict-next-epidemic

## Research Question
The goal of this project is to see if temperature, humidity, precipitation, and vegetation index help predict the total Dengue cases for each (city, year, and weekofyear) in the test set. The project will be evaluated using both the mean absolute error and mean squared error. There will be predictions for both the cities of San Juan, Puerto Rico and Iquitos, Peru. The study will be completed in two steps. The first of which we will use the weather data belonging to each city to train on and then predict the Dengue count in the test set. The second step of the study we will train our models on the weather data of San Juan, and then we will test the models on the Iquitos data. This will give a comparison as to how similar the climates of each city are and provide a baseline of how areas with similar climates may be susceptible to the spread of Dengue. Our group plans to use several types of analyses to help predict the total cases such as linear regression, random forest, and XGBoost. The linear regression will give us a baseline value to compare the estimated cases when using the more advanced models like random forest.
### Exploratory Data Analysis Questions
- Are air temperature and precipitation affected by seasonal changes?
- How does total cases correlate with mean dew point temperature?
- How does humidity level correlate with total cases?
- Does Vegetation Index play any role in disease spread?

## Application of the CRISP-DM Process:

## Dataset
The features in this dataset - dengue_features_train.csv:

We are provided the following set of information on a (year, weekofyear) timescale:

(Where appropriate, units are provided as a _unit suffix on the feature name.)
City and date indicators

    city – City abbreviations: sj for San Juan and iq for Iquitos
    week_start_date – Date given in yyyy-mm-dd format

NOAA's GHCN daily climate data weather station measurements

    station_max_temp_c – Maximum temperature
    station_min_temp_c – Minimum temperature
    station_avg_temp_c – Average temperature
    station_precip_mm – Total precipitation
    station_diur_temp_rng_c – Diurnal temperature range

PERSIANN satellite precipitation measurements (0.25x0.25 degree scale)

    precipitation_amt_mm – Total precipitation

NOAA's NCEP Climate Forecast System Reanalysis measurements (0.5x0.5 degree scale)

    reanalysis_sat_precip_amt_mm – Total precipitation
    reanalysis_dew_point_temp_k – Mean dew point temperature
    reanalysis_air_temp_k – Mean air temperature
    reanalysis_relative_humidity_percent – Mean relative humidity
    reanalysis_specific_humidity_g_per_kg – Mean specific humidity
    reanalysis_precip_amt_kg_per_m2 – Total precipitation
    reanalysis_max_air_temp_k – Maximum air temperature
    reanalysis_min_air_temp_k – Minimum air temperature
    reanalysis_avg_temp_k – Average air temperature
    reanalysis_tdtr_k – Diurnal temperature range

Satellite vegetation - Normalized difference vegetation index (NDVI) - NOAA's CDR Normalized Difference Vegetation Index (0.5x0.5 degree scale) measurements

    ndvi_se – Pixel southeast of city centroid
    ndvi_sw – Pixel southwest of city centroid
    ndvi_ne – Pixel northeast of city centroid
    ndvi_nw – Pixel northwest of city centroid
    
Goal/Target Variable:
    
    total_cases – which lists the number of cases each week in the data 

## Data Preparation/Preprocessing:
1. Importing train data set into jupyter notebook
2. Explore the datatypes and statistical information of the csv
3. Check for percentage of null vales
4. For any missing data that has less than 50% impute the data 
5. Impute based on the median value since most of the data has outliers. Imputing on the mean would skew the data by including the outliers
6. Convert "city" into a categorical variable using label encoder
7. Create a box plot matrix to look at outliers of each numerical variable
8. Drop outliers on variables that have 7 or less outliers 
9. Standardize the variables with outliers using StandardScaler to put them in the same range of values 
10. Put new standardized variables in trans_df dataframe
11. Plot new bow plot matrix to observe any remaining outliers from the standardized variables

## Exploratory Data Analysis:
1. Create a correlation matrix of the new dataframe to observe multicollinearity
2. Add a heat map of the correlation matrix to see the correlation visually
![](images/Heatmap.png)

The lighter the color, the more positively correlated it is with a variable and vice-versa. This map makes it easier to spot multicollinearity. Here, there are many variables that might influence one another. For example, the satellite vegetation indices "ndvi" all seem to influence each other somewhat. The southeast centroid is 0.82 correlated with the southwest centroid. It may be best to keep only one southeast centroid. After running a regression and looking at VIF scores to confirm their influence on each other, one can be dropped from the model.

3. Make a scatter plot matrix to observe trends in the data
4. Create a histogram matrix to look at the distributions, and note any that might need to be later transformed after running a regression
5. Create a mask variable that will hide any features that do not have any correlation coefficient with less than 0.90
6. Apply this mask to the dataframe to see which variables have the most multicollinearity
7. Create a new dataframe "X_clean" that has the variables with multicollinearity above 0.90 dropped from it
8. Run sweetviz package to see association chart
9. Use RandomForestClassifier to see the features that will be most important to keep when running a regression in the next stage
![](images/Randomforest.png)

The random forest model lists the feature importance for each remaining varaible in the data set in descending order. "Weekofyear" is shown as the best overall feature. When running our regression prediction, we can use this to create a threshold value. Any feature with less than 0.045 importance can be thrown out, and the remaining features can be kept. In this case, starting with "reanalysis_specific_humidity_g_per_kg" and below can be dropped before runnning a regression.

10. Marked a threshold of 0.045 and less to remove least important features in the futures
11. Make hierarchical cluster of each remaining variable to spot relationships, and visualize variable importance
![](images/Dendrogram.png)

The dendrogram above gives a visual on each feature, and depicts clustering relationship based on how related they are to other features. We can view the correlation of the variables and see that variable such as "ndvi" and its directions are all quite correlated with two clusters next to each other. After running a prediction model, only one may need to be kept to best model a case of dengue.

12. Generate second hierarchical cluster to see variable relationships after removing variables that had correlation coefficients with values less than 0.90

![](images/Dendrogram2.png)

## EDA Results:
From the scatter plot matrix, we see several variables that are positively correlated with the total case count. These variables are: weekofyear, reanalysis_air_temp_k, reanalysis_avg_temp_k, reanalysis_dew_point_temp_k, reanalysis_min_air_temp_k, station_avg_temp_c, and station_min_temp_c. When holding each other variable equal, these variables will produce a larger amount of dengue cases. It is interesting to note that more total cases occur as it gets later in the year. This coincides with the rainy season in both cities. We can also see that typically as the air temperature, dew point, and humidity increase the more cases that arise. These results are consistent when factoring in the random forest classifier feature importance bar chart. We again see that weekofyear is the most significant feature in predicting Dengue case, with precipitation and vegetation index also showing high importance.

## VIF Scores
Before running our regression, we imported variance_inflation_factor from statsmodels to calculate VIF scores of the features. This gives us another output value to test for multicollinearity amongst our features. Any features that have VIF scores above 10 can be dropped before running a regression model, as these features have a large amount of influence on another feature and will skew the results.
1. Create trans_df2 by dropping columns in trans_df that had a correlation of 1 from the correlation matrix with another column (columns 3,7,10,15, and 16)
2. Import variance_inflation_factor from statsmodels to calculate remaining VIF scores on the columns
3. Drop any column with VIF greater than 10
4. Create VIF_X_clean dataset after dropping high VIF columns

## Modeling Process
1. Create train/test split variables from the trans_df, X_clean, and VIF_X_clean datasets to use them in modeling
2. (For Part 1) - Use the weather data belonging to each city to train on and then predict the Dengue count in the test set
3. Import LinearRegression, mean_squared_error, and mean_absolute_error from sklearn
4. Run LinearRegression on each of the datasets and view error outputs
5. Import RandomForestRegressor from sklearn
6. Run RandomForestRegressor on each of the datasets and view error outputs
7. Import XGBoost as xgb
8. Run XGBoost on each of the datasets and view error outputs
9. (For Part 2) - Train models on the weather data of San Juan, and then we will test the models on the Iquitos data
10. View value counts of the two cities in trans_df dataframe
11. Create new SJ and IQ train and test data splits for the trans_df, X_clean, and VIF_X_clean datasets
12. Re-run LinearRegression on each of the new datasets and view error outputs
13. Re-run RandomForestRegressor on each of the new datasets and view error outputs
14. Re-run XGBoost on each of the new datasets and view error outputs
15. Tune hyperparameters for XGBoost by running defined function
16. List out the tuned hyperparameters
17. Re-run tuned XGBoost on each of the new datasets and view error outputs

## Machine Learning
After the data pre-processing and EDA phases, we implemented supervised learning algorithms to make the predictions for the total_cases of Dengue in each city. Modeling techniques such as Linear Regression, Random Forest, and XGBoost were implemented. These techniques were applied to three dataframes: trans_df, X_clean, and VIF_X_clean. 

The three dataframes differ in that trans_df consisted of all original features after those with extreme outliers had been standardized. X_clean contained the same features as trans_df, but several were removed that contained correlations greater than 90% with other features based on the correlation matrix. Lastly, VIF_X_clean had the same features as trans_df, but those with VIF scores greater than 10 were dropped from it. Each of the dataframes were used in modeling to see which dataframe would generalize the predictions the best to the test set. 

(Part 1) - Use the weather data belonging to each city to train on and then predict the Dengue count in the test set

Linear Regression was first applied to the three dataframes to set a baseline value for Dengue case predictions. This model is used for finding a linear relationship between the target and one or more predictors. The outcomes for the models for MSE and MAE are as follows:
Trans_df - solely standardized data (1456.89, 21.19)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (1658.74, 20.94)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (1418.00, 19.98)

As we would expect here, the VIF_X_clean dataframe was the best performing linear regression model, as it had all the features with high multicollinearity dropped from it. 

Next, we tried using the ensemble method Random Forest on the three dataframes to see if we could improve our prediction errors. This model can perform both regression and classification tasks with the use of multiple decision trees that make up the Random Forest. The outcomes for the models for MSE and MAE are as follows:
Trans_df - solely standardized data (830.31, 14.80)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (1151.94, 15.68)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (850.64, 15.85)

The best performing dataframe for this model was trans_df. This is most likely due to Random Forest models being able to easily handle outliers and features with multicollinearity, since it is made up of Decision Trees. The further reduction of previous features in the other dataframes was unnecessary. 

Lastly, we tried using the XGBoost method on the three dataframes to see if we could improve our prediction errors. Boosting is an ensemble learning method that combines a set of weak learners (models) into a strong learner (model) to minimize training errors. To perform boosting, a random sample of data is chosen, fitted with a model, and then trained sequentially. This means each model tries to compensate for the weaknesses of the previous one. They compensate for the weaknesses by increasing the weights they place on misclassified data. The outcomes for the models for MSE and MAE are as follows:
Trans_df - solely standardized data (567.39, 13.33)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (794.31, 14.09)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (683.91, 14.98)

The best performing model was again trans_df. This was the best dataframe since features are chosen at random to be used in the modeling. XGBoost is able to incorporate all features within its implementation and use the strongest features to make predictions. The dataframes that had features dropped would not be able to have all features contribute to the learning process. 

In summary, for part one, the best overall model came from using XGBoost as it had the lowest MSE and MAE of (567.39, 13.33) using the trans_df dataframe. The XGBoost results were able to significantly improve upon the baseline case predictions set by the Linear Regression values which were around 20 for the MAE.

(Part 2) - Train models on the weather data of San Juan, and then we will test the models on the Iquitos data

Part 2 of this study was done to see how well similar climates could be used to predict the spread of Dengue. If the San Juan weather data could be used to predict Dengue cases in Iquitos effectively, then we could get a clearer idea on how likely Dengue is to spread at locations with similar climates like Singapore and Southern part of the United States. 

Linear Regression:
Trans_df - solely standardized data (1105.41, 27.37)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (1164.48, 28.91)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (1278.83, 31.75)

After running the linear regression, we see that we do not beat the baseline prediction of approximately 20 MAE. However, the trans_df MAE is still quite low at 27.37 and is still able to generalize the case predictions well. It is also of note, that the MSE is lower than the average MSE of the original linear regression.

Random Forest:
Trans_df - solely standardized data (2523.52, 40.00)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (1826.06, 31.73)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (447.24, 18.96)

This time, after running the Random Forest model, we can beat the baseline prediction score around 20 MAE using the VIF_X_clean dataframe. In this case, removing the high multicollinearity features from the training data helps remove some of the noise for the model when making predictions. 

XGBoost (with tuned hyperparameters):
Trans_df - solely standardized data (4668.50, 64.61)
X_clean - standardized and removal of highly correlated features based on the correlation matrix (175.88, 9.20)
VIF_X_clean - standardized and removal of features with VIF scores greater than 10 (199.90, 10.75)

This model gives the best performance out of any. It has the lowest MSE and MAE values after tuning the hyperparameters. 

In summary, since we can beat the baseline performance of 20 MAE, we can more confidently conclude that Dengue is likely to spread to warm, tropic climates around the globe.

## Evaluation
The two main methods of evaluation for this project were Mean Absolute Error and Mean Squared Error. The MAE provides the average difference observed in the predicted and actual values across the whole test set. It doesn’t matter if the prediction is higher or lower than the actual value, the method just looks at the absolute value. A lower value indicates better accuracy. This is a good evaluation metric to use when outliers are not concerning, although our data set contained many. The MSE was incorporated as an alternative metric that more easily handled outliers to measure performance. This method assigns more weight to bigger error values and takes the average of them. Again, the lower the value the better. When taking both the MAE and MSE values into account we see that the XGBoost model was the most successful at making predictions. This model beat the baseline performance and gave the lowest error values. This is due to the model using all features and weighting the prediction values they make. 

## Future Plan
1. Iteratively include latest data into dataset from different sources.
2. Add some new features which can make the dataset more reliable. For instance, pollution level, environmental elements, etc.
3. Expand the study further to compare the spread of predicted total cases in the United States.  
4. Extract and consolidate vegetation indices for areas in Singapore to include in new predictions.

## Instructions to Use
To use this code for further work you can download the Dengue_KDD file and run it in Google Colab or other python friendly environments. You may follow our groups' steps in generating our results by following the numbered steps listed out above - they are listed in detail with headers for each part of the CRISP-DM process. 
