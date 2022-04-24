# Trees, Air Quality, and Wealth Distribution in New York City

Trees are important to New York City for beautification as well as for air quality -- and for host of other impacts that are being studied. Decennially, since 1995, the street trees of New York -- those planted on and near sidewalks -- have been surveyed by the Department of Parks and Recreation. This project began with curiosity about what these Tree Censuses could reveal about quality of life throughout the five boroughs. Environmental justice is a topic of global urgency, as climate change impacts communities worldwide unequally, with marginalized and low-wealth populations often bearing a disproportionate burden of environmental degradation, pollution, and changing weather patterns. The investigation presented here examines the relationship between environmental quality and income distribution, using tree censuses from 2005 and 2015, and metrics of pollution and traffic density from 2009-2017, as features in Random Forest Regressor model to predict median household income. The level of accuracy of the model reflects the strong relationship among these variables; the model's error reflects the insufficiency of these particular environmental metrics to fully explain the diversity of income across geographic micro-districts of the city.


# Problem Questions
What is the relationship between proximity to trees and green space -- as measured by tree density, tree health, and other available metrics -- and income in New York City? Along with measures of air quality, including pollutants which trees are understood to help reduce, can these metrics of green space be used to accurately predict median household income? Where, geographically, does such a model perform best and worst?


# Data Sources
[Tree census 2015](https://data.world/johnsnowlabs/nyc-street-tree-census-2015)

[Tree census 2005](https://data.cityofnewyork.us/Environment/2005-Street-Tree-Census/29bw-z7pj)

[Air quality, by community district](https://data.cityofnewyork.us/Environment/Air-Quality/c3uy-2p5r)

[Percent of households in proximity to a park, by community district](https://app.coredata.nyc/?mlb=false&ntii=prox_park_pct&ntr=Community%20District&mz=11&vtl=https%3A%2F%2Fthefurmancenter.carto.com%2Fu%2Fnyufc%2Fapi%2Fv2%2Fviz%2F98d1f16e-95fd-4e52-a2b1-b7abaf634828%2Fviz.json&mln=true&mlp=true&mlat=40.718&ptsb=&nty=2017&mb=roadmap&pf=%7B%22subsidies%22%3Atrue%7D&md=table&mlv=false&mlng=-73.936605&btl=Borough&atp=neighborhoods#)

[Household income, by census tract](https://datausa.io/profile/geo/new-york-ny#income_geo)

### Shapefiles:
[2010 Census Tracts](https://data.cityofnewyork.us/City-Government/2010-Census-Tracts/fxpq-c8ku)

[Community Districts](https://www1.nyc.gov/site/planning/data-maps/open-data/districts-download-metadata.page)


# Methodology
The 2005 and 2015 Tree Censuses include observations on each of approximately 570,000 street trees in New York City. Using Geopandas and the publicly available shapefiles listed above, and the latitude and longitude for each tree counted in the censuses, I reconfigured the individual tree measures into aggregates by 2010 census tract, in order that each of the approximately 2100 census tracts could serve as an observation in my model. Household income, the target variable in the model, is estimated for each census tract in the American Community Survey; I used the 2017 data in order to maintain a close temporal relationship between the span of tree measures, environmental measures, and the target income variable.

Other features examined and used for modeling are air quality measures and household access to parks. Average seasonal measures of pollutants -- fine particulate matter (PM 2.5), nitrogen dioxide (NO2), sulfur dioxide (SO2), and ozone (O3) -- from nine consecutive years (2009-2017), as well as traffic density as measured in 2005 and 2016, are recorded not by census tract but by larger Community Districts. Once again bringing in publicly available district shapefiles and using Geopandas, I matched each tree to its overlapping (or most closely overlapping) community district and allowed that district's air quality and park access measures to stand in as a measure for the census tract.

After doing exploratory data analysis using Geopandas and Tableau, I attempted fitting several models to the data. Among the various ensemble models from the scikit-learn library that I tried, the Random Forest Regressor performed best in predicting household income based on the features included. I tuned the model using GridSearchCV and ran the model with several different selections of features. (I also tried using PCA, which did not yield a better result than keeping the features untransformed and selecting features based on my observations in EDA). 

# Results 

The Random Forest model predicted with less error than the null model (i.e. mean household income), but it retained high variance throughout many iterations. The R2 score on the training set was 0.9458; the R2 score on the testing set was 0.6509 -- meaning that 65% of variance in median household income is explained by the independent variables in the model. The model's mean squared error showed a 65.15% improvement from the null.

The variance in the model -- as seen through a residual plot categorized by borough (below) -- shows that the model predicted less accurately in wealthier census tracts; it predicted best in the Bronx and worst in Manhattan.

<img src="visuals/resid_plot.png">


# Applications and Further Steps
Predicting potential impact of tree-planting, pollution-reduction efforts on geographic income stratification 
Identifying areas of high risk for adverse health effects of climate change/pollution



# Data Dictionary

| Name | Data type | Description |
|------|-----------|-------------|
| borough | integer | borough location: 1 = Manhattan, 2 = Bronx, 3 = Brooklyn, 4 = Queens, 5 = Staten Island |
| boro_ctlabel | string | unique identifier comprised of borough number and census tract number |
| tract_area | float | area of census tract, in automatically generated units |
| mean_tree_diameter | float | mean recorded diameter of trees in 2015; dead trees are recorded as 0 |
| alive | float | percent of trees marked as alive in 2015 |
| health_good | float | percent of trees marked as having good health (subjective) in 2015 |
| health_fair | float | percent of trees marked as having fair health (subjective) in 2015 |
| health_poor | float | percent of trees marked as having poor health (subjective) in 2015 |
| health_none | float | percent of trees not given a subjective health rating in 2015 |
| BoroCD | integer | New York City Community District containing the census tract (in case of multiple overlaps, this is the CD in which most of the census tract area falls |
| CD_name | string | name of community district |
| tree_count | integer | number of unique trees in the tract in 2015 |
| tree_density | float | 2015 tree count divided by tract area times 10^6 |
| n_species | integer | number of unique tree species in the tract in 2015 |
| alive_05 | float | percent of trees marked as alive in 2015 |
| tree_count_05 | integer | number of unique trees in the tract in 2005 |
| tree_density_05 | float | 2005 tree count divided by tract area times 10^6 |
| n_species_05 | integer | number of unique tree species in the tract in 2005 |
| tree_count_chg | float | change in tree count from 2005-2015, as a percentage of 2005 figure |
| tree_dens_chg | float | change in tree density from 2005-2015, as a percentage of 2005 figure |
| n_species_chg | float | change in tree species count from 2005-2015, as a percentage of 2005 figure |
| alive_chg | float | change in percentage of living trees from 2005-2015, as a percentage of 2005 figure |
| 08-09W_FPM | float | mean fine particulate matter (PM2.5), mcg per cubic meter, winter 2008-09, by community district |
| 09-10W_FPM | float | "  2009-10 |
| 10-11W_FPM | float | "  2010-11 |
| 11-12W_FPM | float | "  2011-12 |
| 12-13W_FPM | float | "  2012-13 |
| 13-14W_FPM | float | "  2013-14 |
| 14-15W_FPM | float | "  2014-15 |
| 15-16W_FPM | float | "  2015-16 |
| 16-17W_FPM | float | "  2016-17 |
| Wint_FPM_09-17Chg | float | change in winter PM2.5 level from 2008-09 to 2016-17, as pct of 08-09 figure |
| 09S_FPM | float | mean fine particulate matter (PM2.5) mcg per cubic meter, summer 2009, by community district |
| 10S_FPM | float | "  2010 |
| 11S_FPM | float | "  2011 |
| 12S_FPM | float | "  2012 |
| 13S_FPM | float | "  2013 |
| 14S_FPM | float | "  2014 |
| 15S_FPM | float | "  2015 |
| 16S_FPM | float | "  2016 |
| 17S_FPM | float | "  2017 |
| Summ_FPM_09-17Chg | float | change in summer fine particulate matter (PM2.5) level from 2009 to 2017, as pct of 2009 figure |
| 08-09W_NO2 | float | mean nitrogen dioxide level, ppb, winter 2008-09, by community district |
| 09-10W_NO2 | float | "  2009-10 |
| 10-11W_NO2 | float | "  2010-11 |
| 11-12W_NO2 | float | "  2011-12 |
| 12-13W_NO2 | float | "  2012-13 |
| 13-14W_NO2 | float | "  2013-14 |
| 14-15W_NO2 | float | "  2014-15 |
| 15-16W_NO2 | float | "  2015-16 |
| 16-17W_NO2 | float | "  2016-17 |
| Wint_NO2_09-17Chg | float | change in winter nitrogen level from 2008-09 to 2016-17, as pct of 08-09 figure |
| 09S_NO2 | float | mean nitrogen dioxide level, ppb, summer 2009, by community district |
| 10S_NO2 | float | "  2010 |
| 11S_NO2 | float | "  2011 |
| 12S_NO2 | float | "  2012 |
| 13S_NO2 | float | "  2013 |
| 14S_NO2 | float | "  2014 |
| 15S_NO2 | float | "  2015 |
| 16S_NO2 | float | "  2016 |
| 17S_NO2 | float | "  2017 |
| Summ_NO2_09-17Chg | float | change in nitrogen dioxide level from summer 2009 to summer 2017, as pct of 2009 figure |
| 08-09W_SO2 | float | mean sulfur dioxide level, ppb, winter 2008-09, by community district |
| 09-10W_SO2 | float | "  2009-10 |
| 10-11W_SO2 | float | "  2010-11 |
| 11-12W_SO2 | float | "  2011-12 |
| 12-13W_SO2 | float | "  2012-13 |
| 13-14W_SO2 | float | "  2013-14 |
| 14-15W_SO2 | float | "  2014-15 |
| 15-16W_SO2 | float | "  2015-16 |
| SO2_09-16Chg | float | change in winter sulfur dioxide level from 2009-09 to 2015-16, as pct of 2008-09 figure |
| 09S_O3 | float | mean ozone level, ppb, summer 2009, by community district |
| 10S_O3 | float | "  2010 |
| 11S_O3 | float | "  2011 |
| 12S_O3 | float | "  2012 |
| 13S_O3 | float | "  2013 |
| 14S_O3 | float | "  2014 |
| 15S_O3 | float | "  2015 |
| 16S_O3 | float | "  2016 |
| 17S_O3 | float | "  2017 |
| O3_09-17Chg | float | change in summer ozone level from 2009 to 2017, as pct of 2009 figure |
| 05_TD_Vehicle | float | Traffic density, all vehicles: estimated millions of miles driven, 2005 (by community district)|
| 16_TD_Vehicle | float | "  2016 |
| 16_TD_Car | float | Traffic density, cars: estimated millions of miles driven, 2016 (by community district)|
| 05_TD_Car | float | "  2005 |
| 16_TD_Truck | float | Traffic density, trucks: estimated millions of miles driven, 2016 (by community district)|
| 05_TD_Truck | float | "  2005 |
| TD_Car_05-16Chg | float | Change in traffic density, cars, from 2005-16, as pct of 2005 figure |
| TD_Truck_05-16Chg | float | Change in traffic density, trucks, from 2005-16, as pct of 2005 figure |
| TD_Vehicle_05-16Chg | float | Change in traffic density, all vehicles, from 2005-16, as pct of 2005 figure |
| near_park | float | percent of households within 1/4 mile of a park (by community district)|
| hh_income | float | median household income, 2017, by census tract |
