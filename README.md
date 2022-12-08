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

Humans cause approximately 85% of wildfires which leads to billions of dollars worth of damage every year in the United States alone. By predicting where ongoing wildfire will spread, we can mitigate these significant costs by optimally allocating resources to start suppression efforts.

## Data-Collection

For each fire, the dataset has tweleve defining features:
* previous fire mask
* energy release component (fire strength)
* elevation
* drought
* minimum temperature
* maximum temperature
* wind direction
* vegetation
* precipitation
* population
* humidity
* wind speed

The data is presented as 64 km x 64 km grids with 1 km resolution. We randomly crop a 32 km x 32 km square from each region for training, to reduce the computational complexity. We use the middle 32 km x 32 km square from each region for validation and testing. 

Only ~1% of the land in each 64 km x 64 km grid is on fire. Approximately 98% of the total dataset is not on fire. The remaining portion, up to 2.4%, of our data is missing, usually due to smoke or cloud cover. This data is not used when evaluating the accuracy of the model.


## Exploratory-Data-Analysis

The most important features in predicting wildfire spread are:
* the previous fire mask
* wind direction
* wind speed
* energy release component


## Predictability-of-Wildfire-Spread

This problem is challenging because approximately 98% of the data is not on fire. We also make the assumption that no fire suppression efforts have been made from day to day.

## Modeling-Approach

The logistic regression and random forest models lacked spatial awareness of the fire spread. Like a feed forward neural network, they treat each 1 by 1 km sample as independent. For example, if wind is coming from the north, these algorithms have no information on if fire is north of you. They are unable to accurately predict whether the fire will spread to you. These models can work as a baseline, but they are not going to be very helpful. 

Neural network uses spatial information along with 2.3 million parameters to predict whether each 1x1 km area will be on fire or not.


## Conclusions-and-Future-Directions
