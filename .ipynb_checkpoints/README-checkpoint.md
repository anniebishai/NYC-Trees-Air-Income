# Trees, Air Quality, and Wealth Distribution in New York City

Trees are important to New York City for beautification as well as for air quality -- and for host of other impacts that are being studied. Decennially, since 1995, the street trees of New York -- those planted on and near sidewalks -- have been surveyed by the Department of Parks and Recreation. This project began with curiosity about what these Tree Censuses could reveal about quality of life throughout the five boroughs. Environmental justice is a topic of global urgency, as climate change impacts communities worldwide unequally, with marginalized and low-wealth populations often bearing a disproportionate burden of environmental degradation, pollution, and changing weather patterns. The investigation presented here examines the relationship between environmental quality and income distribution, using tree censuses from 2005 and 2015, and metrics of pollution and traffic density from 2009-2017, as features in Random Forest Regressor model to predict median household income. The level of accuracy of the model reflects the strong relationship among these variables; the model's error reflects the insufficiency of these particular environmental metrics to fully explain the diversity of income across geographic micro-districts of the city.


# Problem Questions
What is the relationship between proximity to trees and green space -- as measured by tree density, tree health, and other available metrics -- and income in New York City? Along with measures of air quality, including pollutants which trees are understood to help reduce, can these metrics of green space be used to accurately predict median household income? Where, geographically, does such a model perform best and worst?


# Methodology
The 2005 and 2015 Tree Censuses include observations on each of approximately 570,000 street trees in New York City. Using Geopandas and the publicly available shapefiles linked below, and the latitude and longitude for each tree counted in the censuses, I reconfigured the individual tree measures into aggregates by 2010 census tract, in order that each of the approximately 2100 census tracts could serve as an observation in my model. Household income, the target variable in the model, is estimated for each census tract in the American Community Survey; I used the 2017 data in order to maintain a close temporal relationship between the span of tree measures, environmental measures, and the target income variable.

Other features examined and used for modeling are air quality measures and household access to parks. Average seasonal measures of pollutants -- fine particulate matter (PM 2.5), nitrogen dioxide (NO2), sulfur dioxide (SO2), and ozone (O3) -- from nine consecutive years (2009-2017), as well as traffic density as measured in 2005 and 2016, are recorded not by census tract but by larger Community Districts. Once again bringing in publicly available district shapefiles and using Geopandas, I matched each tree to its overlapping (or most closely overlapping) community district and allowed that district's air quality and park access measures to stand in as a measure for the census tract.

After doing exploratory data analysis using Geopandas and Tableau, I attempted fitting several models to the data. Among the various ensemble models from the scikit-learn library that I tried, the Random Forest Regressor performed best in predicting household income based on the features included. I tuned the model using GridSearchCV and ran the model with several different selections of features. (I also tried using PCA, which did not yield a better result than keeping the features untransformed and selecting features based on my observations in EDA). 

# Results 

The Random Forest model predicted with less error than the null model (i.e. mean household income), but it retained high variance throughout many iterations. The R2 score on the training set was 0.9458; the R2 score on the testing set was 0.6509 -- meaning that 65% of variance in median household income is explained by the independent variables in the model. The model's mean squared error showed a 65.15% improvement from the null.

The variance in the model -- as seen through a residual plot categorized by borough (below) -- shows that the model predicted less accurately in wealthier census tracts; it predicted best in the Bronx and worst in Manhattan. This suggests the need for better feature selection and/or engineering; it also reinforces the implication of the .65 R2 score, which is that there is substantial variance in income in New York City that cannot be accounted for by the variables used.

![residual plot](Visuals/resid_plot.png?raw=true)


# Applications and Further Steps
Predicting potential impact of tree-planting, pollution-reduction efforts on geographic income stratification 
Identifying areas of high risk for adverse health effects of climate change/pollution
