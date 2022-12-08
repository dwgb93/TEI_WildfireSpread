# TEI_WildfireSpread
Predictions of where a fire will spread in the upcoming day are essential to wildfire emergency response, leading to optimal allocation of resources and quick response to fire activity.

Our goal is to use the Next Day Wildfire Spread dataset to predict where wildfires will spread, identifying areas for optimal resource allocation and evacuation, hopefully saving lives.

F. Huot, R. L. Hu, N. Goyal, T. Sankar, M. Ihme, and Y.-F. Chen, “[Next Day Wildfire Spread: A Machine Learning Data Set to Predict Wildfire Spreading from Remote-Sensing Data](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9840400)”, IEEE Transactions on Geoscience And Remote Sensing, 60, 2022.



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

Wildfires endanger lives, property, and the environment. Unfortunately, humans are the cause for approximately 85% of wildfires leading to billions of dollars worth of damage every year in the United States alone. By predicting where ongoing wildfire will spread, we can mitigate these significant costs by optimally allocating resources to start evacuation and suppression efforts.

Using data of wildfires in the United States from 2012 to 2020 (Huot et al., 2022), the following questions can be answered:
* Which features are most important in wildfire spread prediction?
* How accurately can we predict where wildfires will spread?


## Data-Collection

By accessing the remote sensing technologies, Moderate Resolution Imaging Spectroradiometer (MODIS), Visible Infrared Imaging Radiometer Suite (VIIRS), and Shuttle Radar Topography Mission (SRTM); and combining the data using Google Earth Engine, the dataset consists of 11 environmental features: 
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

The dataset also has an additional feature that includes the previous fire mask making the dataset have 18,545 fire events which are presented as 64 km x 64 km grids with 1 km resolution. We randomly crop a 32 km x 32 km square from each region for training, to reduce the computational complexity. We use the middle 32 km x 32 km square from each region for validation and testing. 

Only ~1% of the land in each 64 km x 64 km grid is on fire. Approximately 98% of the total dataset is not on fire. The remaining portion, up to 2.4%, of our data is missing, most likely due to smoke or cloud cover. This data is not used when evaluating the accuracy of the model.


## Exploratory-Data-Analysis

The most important features that predict wildfire spread in the logistic regression model is:



And in the random forest model:



## Predictability-of-Wildfire-Spread

Predicting wildfire spread from this dataset is challenging because approximately 98% of the data is not on fire. We also make the assumption that no fire suppression efforts have been made.  

## Modeling-Approach

We used 3 models:
1. Sklearn classifiers: Logistic Regression and Random Forest
2. TensorFlow: Convolutional Neural Network. 

| Model | Precision | Recall  | AUC (PR) |
|:--------:|:-------:|:------:|:------:|
| Logistic Regression    | 38.48*   | 26.96	   | 19.45	   |
| Random Forest    | **39.72***  | 26.18	  | 20.67	  |
| Neural Network    | 32.35   | **41.47** | **27.86** |

Table: A Caption


Precision and recall was used to determine the locations that will be on fire the next day.

The logistic regression and random forest models lacked spatial awareness of the fire spread. Like a feed forward neural network, they treat each 1 by 1 km sample as independent. For example, if wind is coming from the north, these algorithms have no information on if fire is north of you. They are unable to accurately predict whether the fire will spread to you. These models can work as a baseline, but they are not going to be very helpful. 

Neural network uses spatial information along with 2.3 million parameters to predict whether each 1x1 km area will be on fire or not.


## Conclusions-and-Future-Directions

Wildfires are an inevitable part of our lives due to humans being the cause of 85% of fires. Predicting where the wildfire will spread helps rescue teams to distribute resources accordingly. Predicting wildfire spread  allows emergency rescue teams and citizens to properly prepare evacuation and relief strategies. 

In the future, these models could be applied to international data to predict wildfire spread in those regions. The models could be improved upon by accounting for existing fire suppression efforts. 

These wildfire sprread prediction models could potentially save the United States hundreds of thousands of dollars in suppression efforts.

