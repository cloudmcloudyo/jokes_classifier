# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Project 3: Reddit NPL Challenge

### Problem Statement
---

Buying a home is stressful, especially for first time buyers. This project sets out to help make their life easier. To be more specific, the project aims to build a prediction model for the sale price of a house based on the Ames dataset, which contains information of 2051 houses in Ames, Iowa from 2006 to 2010. The model should achieve considerable accuracy to provide reliable price estimate reference for the home buyers and should also serves as a guide to indicate important features when it comes to house purchasing.




### Data Cleaning
---

The data provided has 81 features and requires substaintial cleaning due to a great number of missing values, redundancies, inappropriate data types and outliers. For missing values, I dropped the columns with over 80% missing values. For those I kept, I found out that a good amount of them were just the features that specific house doesn't have. So I imputed them with 0. For the rest, I imputed mean for numerical features and the most frequent value for categorical features (including ordinal categorical features that have integer as their value output). 

I then fixed inappropriate data types. Notably the non-ordinal category features whose values are integer, such as ms subclass, month sold, year sold, other year values in the dataset. I also converted the values of ordinal categorical features into numbers rather than strings.

I also fixed some obvious data error, for example the garage year built has a max value of 2207, which is likely a typo. As for the outliers, I dropped two houses that has unusually large square footage however very lower sale price. 




### Feature Engineering
---
The features provided have some redundancies and will possibly lead to multicollinearity in modeling. Therefore I consolidated these features and created 1) total square footage that sums up total ground living area sf, total basement sf, and garage are sf; 2) total bath that sums up all full bath and half bath including the ones in the basement. I considered half bath as one bath rather than half to avoid float numbers. 3) total bedrooms. 

Notice that year values are tricky to work with as they are categorical but in numerical form, I created age columns to solve this issue. I engineered age since the house was built, since it was last remodeled and since the garage was built so it could be more straightforward to work with. 

In addition, I engineered a feature multiplying two features that are most strongly correlated with the sale price: total square footage and overall quality.


### Data Dictionary
---

The data dictionary below contains the features I used for my production model.

|Feature|Type|Dataset|Description|
|---|---|---|---|
|**total_sqft**|*integer*|train|total square footage including the total ground living area, basement and garage, excluding any patio area|
|**total_bath**|*integer*|train|total bathroom number including full bath, half bath, basement full bath and half bath|
|**total_bedroom**|*integer*|train|total bedroom number |
|**neighborhood**|*object*|train|physical locations within Ames city limits|
|**overall_qual**|*integer*|train|overall material and finish quality (scale 1 to 10)|
|**exter_qual**|*integer*|train|exterior material quality (scale 1 to 5)|
|**kitchen_qual**|*integer*|train|kitchen quality (scale 1 to 5)|
|**bsmt_qual**|*integer*|train|basement quality (scale 1 to 5)|
|**fireplace_qu**|*integer*|train|fireplace quality (scale 1 to 5)|
|**garage_finish**|*integer*|train|interior finish of the garage (scale 0 to 3)|
|**fireplaces**|*integer*|train|number of fireplaces|
|**mas_vnr_area**|*integer*|train|masonry veneer area in square feet|
|**garage_cars**|*integer*|train|size of garage in car capacity|
|**age_sold**|*integer*|train|age when sold since built |
|**age_since_remodl**|*integer*|train|age since last remodel or addition|
|**yr_sold**|*object*|train|year the house was sold|
|**total_sqft * overall_qual**|*integer*|train|the value of total square feet multiplied by overall quality|
|**overall_cond**|*integer*|train|overall condition rating (scale 1 to 10)|
|**ms_subclass**|*object*|train|the building class|
|**saleprice**|*integer*|train/ test|sale price of the house|


### Exploratory Data Analysis
---
The distribution of our target variable is right skewed, which is understandable considering fewer people buy expensive homes. However the skewed distribution might affect the homoscedasticity of our model in the later stage. Therefore I'm considering performing log transformation on the variable.   

![](./charts/saleprice_hist.png)


### Feature Selection
---

Due to the large number of features, instead of plotting a heatmap, I did two bar charts that list the top 10 features that show the strongest positive correlation to the sale price and the top 5 strongest negative. Most of the features have an absolute correlation rate over 0.5, which is very significant. These are the features I would always prioritize in considering to include in my models.

![](./charts/top10_pos_corr.png) ![](./charts/top5_neg_corr.png)


By plotting the correlation of the intersection of total square footage and overall quality, I found a sifnificant strong linear correlation between the feature and sale price. Therefore I would strongly consider using this feature especially when improving my model.

![](./charts/intersection.png)

### Modeling
---

7 models were chosen to be showcased in this project. My actual number of modeling is a lot higher but these seven in my opinion will best serve as the road map to my best/production model. The model goes from simple to complex. Each model is to some extent reliant on the previous ones. I started with a null model which used the mean sale price to predict all testing data. After established the baseline, I fitted linear regression model with the features I believe to be most relevant to the sale price. To further imporve the performance of the model, I fitted the dataset to regularization models such as LASSO and Ridge, and performed log transformation. My best performed model used intersectioning of features and log transformation. The reason why I fine tuned my model step by step is that there will be trade-offs in the interpretation when the models get complex. This project is meant to be presented to a non-techinal audience therefore an ultra complex model may be hard for them to understand. That is also why I chose a log transformed model over LASSO and Ridge.


### Evaluation
---
The evaluation of the modeling are divided into two parts. Online there is a Kaggle competition which will evaluate the RMSE score of the model  with 70% of the testing data. My best model scored a RMSE of 21,204.55 on Kaggle which ranks 3rd out of 48 contestants. 

Locally, the model I used with train-test-split and log transformation has the best RMSE, followed by the improved log transformation and OLS model. My lasso and ridge model didn't help decrease my RMSE, however they both have over 90% in R squared score, which means that my model fits over 90% of the data. That is a relatively accurate model. I decided to use the lasso model for my production model despite the fact that it doesn't have the highest score. Based on the features I selected, I believe the regularization would help treaing all the coefficients fair. Considering the goal of this project and the audience, the lasso model would be a good combination of model performance and intepretation.  T


![](./charts/predvstrue.png)


Above is the chart showcasing the distribution of the residuals of the production model I selected. Overall I would conclude that my model has homoscacediticy especially in the 100k to 300k house sale price range. As the sale price goes even higher, the residual starts to fan out.

### Conclusion & Findings
---
Should consider a combined force of total square footage & overall quality
Houses with unusually large square footage might have smaller margin
Stone Brook, Crawford, Clear Creek, Green Hill, and Northridge Heights are the most desirable/ expensive neighborhoods
Iowa DOT and Rail Road,  Old Town, Northwest Ames, College Creek, and Meadow Village are the most affordable neighborhoods



### Limitations & Future Studies
---

This project only scratched the surface of modeling possibilities with the data. Below are some limitations of the project:
1. Due to georaphic discrepancies, I don't believe this model is applicable to other regions especially those whose dollar amount for square footage is significantly higher than  
2. Since my model used lasso regularization, I standardized the data before fitting them to the model so as to keep them at the same scale. It helps making my coefficients more referencable, however, it also makes it hard for me to interpret the exact impact of each feature since they are not the original values anymore. 
3. Although a lot of efforts were made to reduce the number of features, my production model still has 60 columns counting all the dummfied variables. Seems to me still too many features. I would like to explore a more concise model that is as well-fit.
4. The model did not examine the statistical significance of the features selected. As a result, the result of some coefficient magnitude may be misleading. For example, there could be a neighborhood that has very few house information and their pattern of sale price could be very biased. Therefore I would suggest cross referencing the p-value with the coefficients.
5. My problem statement is focused on residentail homes. However the original training data contains non-residential type such as agricultural or industrial properties, which I didn't drop in consideration of the consistancy with the testing data. So for my next step, I would look into dropping these values to see if they will help improve the performance of my model.