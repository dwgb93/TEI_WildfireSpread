# TEI_WildfireSpread
Predictions of where a fire will spread in the upcoming day are essential to wildfire emergency response, leading to optimal allocation of resources and quick response to fire activity.

Our goal is to use the Next Day Wildfire Spread dataset to predict where wildfires will spread, identifying areas for optimal resource allocation and evacuation, hopefully saving lives.

F. Huot, R. L. Hu, N. Goyal, T. Sankar, M. Ihme, and Y.-F. Chen, “[Next Day Wildfire Spread: A Machine Learning Data Set to Predict Wildfire Spreading from Remote-Sensing Data](https://arxiv.org/pdf/2112.02447.pdf)”, arXiv preprint, 2021.



<!-- ## About Team Mahogany
Team members: -->

# Table of Contents
1. [Introduction](#Introduction)
2. [Data Collection](#Data-Collection)
3. [Exploratory Data Analysis](#Exploratory-Data-Analysis)
4. [Predictability of Wildfire Spread](#Predictability-of-Wildfire-Spread)
5. [Modeling Approach](#Modeling-Approach)
6. [Conclusions and Future Directions](#Conclusions-and-Future-Directions)
7. [Description of Repository](#Description-of-Repository)

## Introduction

Humans cause approximately 85% of wildfires leading to billions of dollars worth of damage every year in the United States alone. By predicting where ongoing wildfire will spread, we can mitigate these significant costs by optimally allocating resources to start suppression efforts.

## Data-Collection

For each fire, the dataset has tweleve defining features:
* Previous fire mask
* ERC
* Elevation
* Drought
* Minimum temperature
* Maximum temperature
* Wind direction
* Vegetation
* Precipitation
* Population
* Humidity
* Wind speed

The data is presented as 64 km x 64 km grids with 1 km resolution. We randomly crop a 32 km x 32 km square from each region for training, to reduce the computational complexity. We use the middle 32 km x 32 km square from each region for validation and testing. 

Only ~1% of the land in each 64 km x 64 km grid is on fire. Approximately 98% of the total dataset is not on fire. The remaining portion, up to 2.4%, of our data is missing, usually due to smoke or cloud cover. This data is not used when evaluating the accuracy of the model.


## Exploratory-Data-Analysis

The most important features in predicting wildfire spread are:
* the previous fire mask
* wind direction
* wind speed
* energy release component



## Predictability-of-Wildfire-Spread


## Modeling-Approach


## Conclusions-and-Future-Directions
