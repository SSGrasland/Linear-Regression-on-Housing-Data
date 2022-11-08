# Using Linear Regression on Housing Data to Develop a Housing Communty
#### By Salome Grasland¶
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
In addition to our original dataset 3 columns were constructed: hardiness zones (‘zones’), square footage of lawn (‘sqft_lawn’), and square footage of lawn for the nearest 15 homes (‘sqft_lawn15’). The latter two columns were engineered based on values taken from the sqft_living and sqft_lot columns. The purpose of these columns is to focus on our clients specific issue– building homes that have usable outdoor space in a temperate area.
 
#### King County Hardiness Zones
A hardiness zone is an area that is defined as having certain annual minimum temperatures, a measure which is of concern to the survival of many plants and livestock. For this project we will be using the United States Department of Agriculture (USDA) Hardiness Zone map of King County and limiting the dataset to homes in zone 8b– King County’s most temperate zone. This is done by constructing a zone column based on values taken from the longitude column.
A hardiness zone is an area that is defined as having certain annual minimum temperatures, a measure which is of concern to the survival of many plants and livestock. For this project we will be using the United States Department of Agriculture (USDA) Hardiness Zone map of King County and limiting the dataset to homes in zone 8b– King County’s most temperate zone. This is done by constructing a zone column based on values taken from the longitude column.

 
## Data Analysis
Our variables do not have a normal distribution and the scales are not consistent. Our independent variable ‘price’ has a significant positive skew which we will need to adjust before modeling.
 
A heatmap of correlation was created to see what variables most correlated with our target variable price. This was also done to check for multicollinearity of features (i. E. if two variables are highly correlated with one another, like the number of bathrooms and bedrooms.) 
 
 
We also wanted to check that the variables used for modeling passed the assumptions of linear regression. These assumptions are:
-The variable should have a linear relationship with price.
-The variable should be homoscedastic meaning that an equal amount of variance should be seen around the regression line.
-There should be a normal distribution.
 
 

## Modeling
For modeling OLS regression was used and two values were looked at in the summary: R-squared and p-value. R-squared is a value that communicates how much of the variability around y ('price' in this case) can be explained by the model.  P-value is used to check the null hypothesis, in this case the null hypothesis was that there is no relationship between price and a chosen variable, if the p-value does not fall below 0.05 the null hypothesis cannot be rejected. 
 
### Model 1
The first model iterated looked at the most correlated features as well as sqft_lawn_prct because this value is of interest to our client.
 
### Model 2
In the second model we replace bathrooms with bedrooms because bathrooms had a high p-value. We need p-values below 0.05 in order to reject the null hypothesis.
 
### Model 3
In the third model we remove sqft_above because it is highly correlated (0.85) with sqft_living. We do not want to have multicollinearity of features in our model because we need to be able to indicate where variance is coming from.
 
### Model 4
For our final model we added the most correlated to price dummy variables 'grade_Excellent', 'view_NONE', 'view_EXCELLENT', and 'grade_Luxury'.
 
### Final model
Model 4 has the highest r-squared value at 0.546. R-squared is a value that communicates how much of the variability around y ('price' in this case) can be explained by the model. In this case it means that 54.6% of the variability in our model can be explained by the variable we included. However, since this model does contain several log transformed variables we will go ahead and run it again using data that hasn't been log transformed to simplify interpreting coefficients. 
 
## Interpreting Coefficients 


sqft_living  
USD263.19   
sqft_living15   
USD113.04   
sqft_lawn     
USD-1.05    
bedrooms     
USD-47,017    
view_EXCELLENT    
USD330,266    
view_NONE    
USD-67,619     
grade_Excellent    
USD595,692    
grade_Luxury    
USD1,139,040     



Interpreting our coefficients we can see for each additional square foot a home has increases the price by 263.19USD. Sqft_living15 takes the average size of the 15 nearest homes. We can see that when the average size of these homes goes up a square foot it increases the overall value by 113.04USD. Each additional bedroom also decreases the value of the home by 47,017USD. 

Of concern to our client is the lawn’s size effect on home value. Here we can see that each additional square foot of lawn decreases the price of the home by 1.05USD. Having an excellent view also makes a substantial difference–adding 330,300USD to the home, whereas no view actually decreases the home's value by 67,620USD. 

Quality or grade of the home has a significant impact on price, with an excellent grade adding 595,692USD and a luxury grade adding 1,139,040USD to the value of the home. 



## Evaluation
### Validating the Model
To evaluate the performance of our model it was cross-validated using a train test split. Cross validating the model allows us to know how well the model would serve if new data was presented. 


### RMSE
Root-mean-square error (RMSE) is a measure that shows the deviation between values predicted by a model and the observed values. The deviations are called residuals when performed over sample data and they are called errors when performed on out-of-sample data. It is a measure of accuracy to compare the predictive abilities of different models for a particular dataset. It is one of the fastest methods of analysis and not as sensitive to outliers. Here we use scikits mean_squared_error feature to obtain the value. 
Of concern is overfitting the model-- this occurs when the model fits the noise of our data, but performs poorly when presented with new data. A good indicator of overfitting is if the training dataset is significantly better than the test dataset. In this case, the Train Root Mean Squared Error was 250,804USD and the Test Root Mean Squared Error was 261,532USD seeing as how our data ranges from 78,000USD to 7,700,000USD a difference of 10,728USD between the train and test RMSE means overfitting is not of significant concern. 
## Conclusion
Our client, Steady, is building a neighborhood of homes for people who want to use their yards. Our analysis indicates that the best predictor of a house’s price in King County, Seattle in zone 8b is 'sqft_lawn', 'sqft_living',  'sqft_living15',  'bedrooms',  'grade_Excellent', 'view_EXCELLENT',  'grade_Luxury',  'view_NONE'. We discovered that certain features had a positive impact on price, such as, the square footage of the home, the square footage of the nearest 15 homes, an excellent or luxury grade, and an excellent view. While features like square footage of lawn, number of bedrooms, and no view had a negative impact on price. Some limitations of our model is that this housing data is from May 2014 to 2015. Meaning that as housing trends change the model may no longer accurately reflect current housing patterns. 
### Recommendation
Our recommendation to Steady is to build a neighborhood of equally larger homes (~2,000 square feet), of excellent quality (quality is also referred to as grade), and an excellent view. An excellent grade means a home has a custom design and high quality cabinet work, wood trim, and highest quality materials. An excellent grade can increase the value of home by 595,692USD 

We recommend building a community of large luxury homes that are at least 2,000 square feet. Large homes are recommended because each additional square foot the home has adds 263.19USD to its value. We also recommend that all the homes built be of similar size. Our analysis shows that when the average size of the 15 nearest homes goes up a square foot it increases their overall value by 113.04USD. This means that a neighborhood of larger homes would be more valuable than a neighborhood of variable size homes. We would also warn our client that fewer bedrooms is favorable because each additional bedroom has the potential to devalue the home by 47,017USD. 

Of concern to our client is the lawn’s size effect on home value. Our analysis shows that each additional square foot of lawn decreases the price of the home by 1.05USD. When designing the layout of the lot Steady needs to carefully consider how much lawn space there is as it will slightly decrease the value of the home. Having an excellent view also makes a substantial difference–adding 330,300USD to the value of the home, whereas no view actually decreases the home's value by 67,620USD. 
## For More Information 
For additional info, contact Salome Grasland at salome.grasland@ncf.edu
## Repository Structure
├── README.md                           <- The top-level README for reviewers of this project    
├── Phase 2 Project.ipynb               <- Narrative documentation of analysis in Jupyter notebook     
├── Phase 2 Project Presentation.pdf    <- PDF version of project presentation     
├── data                                <- Both sourced externally and generated from code     
└── images                              <- Both sourced externally and generated from code      

 
## References 
https://www.kaggle.com/code/henriqueyamahata/boston-housing-with-linear-regression      
https://www.kaggle.com/code/madislemsalu/predicting-housing-prices-in-king-county-usa    
https://github.com/mojo-flat/Linear-Regression-GP-2/blob/main/lin_reg_gp_2_solution.ipynb     
https://data.library.virginia.edu/interpreting-log-transformations-in-a-linear-model/#:~:text=Interpret%20the%20coefficient%20as%20the,variable%20increases%20by%20about%200.20%25.   
https://towardsdatascience.com/encoding-categorical-variables-one-hot-vs-dummy-encoding-6d5b9c46e2db#:~:text=Dummy%20encoding%20also%20uses%20dummy,uses%20k%2D1%20dummy%20variables.       
https://towardsdatascience.com/busted-assumptions-3e224b7706eb    
https://data.library.virginia.edu/interpreting-log-transformations-in-a-linear-model/     
https://info.kingcounty.gov/assessor/esales/Glossary.aspx?type=r  



