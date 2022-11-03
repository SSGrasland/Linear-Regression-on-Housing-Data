# Using Linear Regression on Housing Data to Develop a Housing Communty
#### By Salome Grasland
 
## Business Understanding
A result of the Covid pandemic is that people are now spending more time in their yards, especially those located in urban and suburban areas. The real estate development group, Steady, has approached us to help design a community of homes made for people who enjoy using their land to grow food, have bee hives, and raise chickens. Steady, wishes to build the community of homes in the most temperate zone of King County, Seattle. This kernel uses the King County Housing Dataset to create a Linear Regression Model that Steady can use to know what features the homes they build should contain in order to be financially successful.

## Data Understanding
For this kernel we will be using the King County Housing Dataset from Kaggle. This dataset includes homes sold between May 2014 and May 2015, features 21 columns, and over 21,000 entries. The dataset contains categorical and numerical columns, with data types of integers, objects, and floats. Some of the data types will need to be converted for our analysis. Three columns are missing data– view, waterfront, and yr_renovates– with the latter two missing more than 10% of their values. Null values will need to be dealt with before modeling.

### Column Names and Descriptions for King County Data Set
id - Unique identifier for a house
date - Date house was sold
price - Sale price (prediction target)
bedrooms - Number of bedrooms
bathrooms - Number of bathrooms
sqft_living - Square footage of living space in the home
sqft_lot - Square footage of the lot
floors - Number of floors (levels) in house
waterfront - Whether the house is on a waterfront
view - Quality of view from house
condition - How good the overall condition of the house is. Related to maintenance of house. See the King County Assessor Website for further explanation of each condition code
grade - Overall grade of the house. Related to the construction and design of the house. See the King County Assessor Website for further explanation of each building grade code
sqft_above - Square footage of house apart from basement
sqft_basement - Square footage of the basement
yr_built - Year when house was built
yr_renovated - Year when house was renovated
zipcode - ZIP Code used by the United States Postal Service
lat - Latitude coordinate
long - Longitude coordinate
sqft_living15 - The square footage of interior housing living space for the nearest 15 neighbors
sqft_lot15 - The square footage of the land lots of the nearest 15 neighbors
 
There are over 21,000 entries in this dataset with three columns missing values (waterfront, view, and yr_renovated), we will need to adjust for missing values. There are also three different data types present: object, int64, and float64. We will have to adjust the datatypes in order to model the data.
The homes in this dataset range in price from 78,000 USD to 7,700,000 USD. Some homes are older having been built as early as 1900 with the newest home having been built in 2015. This makes sense since the dataset is limited to homes sold in 2014 to 2015.
## Data Preparation 
We can see that yr_renovated and waterfront are missing more than 10% of their values. In order for our code to function properly we need to remove null values. In this case we will drop these two columns from the dataset.
### Data Engineering
In addition to our original dataset 3 columns were constructed: hardiness zones (‘zones’), square footage of lawn as a percent of total lot size (‘sqft_lawn_prct’), and square footage of lawn as a percent of total lot size for the nearest 15 homes (‘sqft_lawn_prct15’). The latter two columns were engineered based on values taken from the sqft_living and sqft_lot columns. The purpose of these columns is to focus on our clients specific issue– building homes that have usable outdoor space in a temperate area.
 
#### King County Hardiness Zones
A hardiness zone is an area that is defined as having certain annual minimum temperatures, a measure which is of concern to the survival of many plants and livestock. For this project we will be using the United States Department of Agriculture (USDA) Hardiness Zone map of King County and limiting the dataset to homes in zone 8b– King County’s most temperate zone. This is done by constructing a zone column based on values taken from the longitude column.
 
 
## Data Analysis
Our variables do not have a normal distribution and the scales are not consistent. Our independent variable ‘price’ has a significant positive skew which we will need to adjust before modeling.
 
A heatmap of correlation was created to see what variables most correlated with our target variable price. This was also done to check for multicollinearity of features (i. E. if two variables are highly correlated with one another, like the number of bathrooms and bedrooms.) 
 
We also wanted to check that the variables used for modeling passed the assumptions of linear regression. These assumptions are:
-The variable should have a linear relationship with price.
-The variable should be homoscedastic meaning that an equal amount of variance should be seen around the regression line.
-There should be a normal distribution.
 

## Modeling
For modeling OLS regression was used and two values were looked at in the summary: R-squared and p-value. R-squared is a value that communicates how much of the variability around y ('price' in this case) can be explained by the model. P-value is used to check the null hypothesis, in this case the null hypothesis was that there is no relationship between price and a chosen variable, we wanted the p-value to fall below 0.05 so that the null hypothesis could be rejected.
 
### Model 1
The first model iterated looked at the most correlated features as well as sqft_lawn_prct because this value is of interest to our client.
 
### Model 2
In the second model we replace bathrooms with bedrooms because bathrooms had a high p-value. We need p-values below 0.05 in order to reject the null hypothesis.
 
### Model 3
In the third model we remove sqft_above because it is highly correlated (0.85) with sqft_living. We do not want to have multicollinearity of features in our model because we need to be able to indicate where variance is coming from.
 
### Model 4
For our final model we added the most correlated to price dummy variables 'grade_Excellent', 'view_NONE', 'view_EXCELLENT', and 'grade_Luxury'.
 
### Final model
Model 4 has the highest r-squared value at 0.546. R-squared is a value that communicates how much of the variability around y ('price' in this case) can be explained by the model. In this case it means that 54.6% of the variability in our model can be explained by the variable we included.
 
### Interpreting Coefficients
Since price, sqft_living, sqft_living15, and sqft_above were all log transformed we need to adjust the coefficients to accurately interpret them.
 
#### Both dependent and independent variable are log transformed
Since our independent variable ('price') and dependent variable ('sqft_living' and 'sqft_living15') were both log transformed we need to interpret sqft_living and sqft_living15 as a percent increase in the dependent variable for each 1% increase in price. The equation we will use for this is (1.01^x -1)*100 where x is the value of the coefficient.
sqft_living = (1.01^0.543022 - 1)100 = 0.54%
sqft_living15 = (1.01^0.421263 - 1)100 = 0.42%
This means that every 1% increase in the price of the homes value we also see a 0.54% and 0.42% increase in the value of the sqft_living and sqft_living15.

#### Only independent variable is log transformed
Since the rest of our variables were not log transformed we can interpret a 1% increase in price increase or decrease the dependent variable by a (coefficient/100) units. The equation we will use for this is (x * log(1.10)) where x is the value of the coefficient.
sqft_lawn_prct = (-0.368062 * log(1.01)) = -0.00159053347
bedrooms = (-0.033695 * log(1.01)) = -0.0001456
grade_Excellent = (0.528796 * log(1.01)) = 0.00228512517
view_NONE = (-0.200450 * log(1.01)) = -0.00086621937
view_EXCELLENT = (0.307521 * log(1.01)) = 0.00132891318
grade_Luxury = (0.708721 * log(1.01)) = 0.00306264834
This means that for every 1% increase in price our dependent variable decreases the value of sqft_lawn_prct by -0.0016%, bedrooms by -0.0001456, grade_Excellent increases by 0.00228512517, view_None decreases by -0.00086621937, view_EXCELLENT decreases by 0.00132891318, and grade_Luxury increases by 0.00306264834.

## Evaluation
### Validating the Model
To evaluate the performance of our model it was cross-validated using a train test split. Cross validating the model allows us to know how well the model would serve if new data was presented. The model with the best R-squared value was fitted in a train-test split.
 
Root-mean-square error (RMSE) is a measure that shows the deviation between values predicted by a model and the observed values. The deviations are called residuals when performed over sample data and errors when performed on out-of-sample data. It is a measure of accuracy to compare the predictive abilities of different models for a particular dataset. In this case, the Train Root Mean Squared Error was 0.38425 and the Test Root Mean Squared Error was 0.3854 meaning the model generated has good predictive abilities and would perform in a similar fashion if new data was presented. 

## Conclusion
Our client, Steady, is building a neighborhood of homes for people who want to use their yards. Our analysis indicates that the best predictor of a house’s price in King County, Seattle is 'sqft_lawn_prct', 'sqft_living',  'sqft_living15',  'bedrooms',  'grade_Excellent', 'view_EXCELLENT',  'grade_Luxury',  'view_NONE'. We also discovered that certain features had a positive impact on price, such as, sqft_living and sqft_living15, and an excellent grade and view. Some limitation of our model is that features were log transformed to satisfy assumptions of regression. This means that any new data used would have to be processed in the same way. The model also uses housing data from 2014 to 2015. Meaning that as housing trends change the model may no longer accurately reflect current housing patterns. 

### Recommendation
Our recommendation to Steady is to build a neighborhood of equally larger homes with excellent views and excellent grades. An excellent grade means a home has a custom design and high quality cabinet work, wood trim, and highest quality materials. We recommend large homes because for each $10,000 increase in price for the home we see a $5,400 increase in value of the squarefoot of living space. We recommend that all the homes be equally large because the data shows that for each $10,000 increase in price for the 15 nearest homes we also see a $4200 increase in square foot of living for the 15 nearest homes. We also want to warn Steady that increasing lawn size does decrease home value. 
