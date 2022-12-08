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

By accessing the remote sensing technologies and combining the data via Google Earth Engine (GEE), the dataset consists of 11 environmental features: 
* Drought
* Elevation
* Energy Release (fire strength)
* Humidity
* Minimum Temperature
* Maximum Temperature
* Population
* Precipitation
* Vegetation
* Wind Direction
* Wind Speed

The dataset also has an additional feature that includes the previous fire mask making the dataset have 18,545 fire events which are presented as 64 km x 64 km grids with 1 km resolution. We randomly crop a 32 km x 32 km square from each region for training, to reduce the computational complexity. We use the middle 32 km x 32 km square from each region for validation and testing. 

Only ~1% of the land in each 64 km x 64 km grid is on fire. Approximately 98% of the total dataset is not on fire. The remaining portion, up to 2.4%, of our data is missing, most likely due to smoke or cloud cover. This data is not used when evaluating the accuracy of the model.


## Exploratory-Data-Analysis

The most important features that predict wildfire spread in the logistic regression model is:


| Feature | Importance |
|:------:|:------:|
| Previous Fire Boundary | 3.055462 |
| Wind Direction | 2.939174 |
| Min Temp | 0.975030 |
| Population Density | (-) 0.361442 |
| Energy Release | 0.322639 |
| Max Temp | (-) 0.292365 |
| Elevation | 0.181095 |
| Precipitation | (-) 0.162624 |
| Drought  | (-) 0.135867 |
| Wind Velocity | 0.069142 |
| Humidity | (-) 0.024723 |
| Vegetation | (-) 0.000200 |


And in the random forest model:


| Feature | Importance |
|:------:|:------:|
| Previous Fire Boundary | 0.627122 |
| Energy Release | 0.112019 |
| Elevation | 0.057490 |
| Min Temp | 0.035959 |
| Population Density | 0.028470 |
| Max Temp | 0.027971 |
| Humidity | 0.027614 |
| Drought | 0.024936 |
| Vegetation | 0.022217 |
| Wind Velocity | 0.017163 |
| Wind Direction | 0.013778 |
| Precipitation | 0.005260 |


## Predictability-of-Wildfire-Spread

Predicting wildfire spread from this dataset is challenging because approximately 98% of the data is not on fire. We also make the assumption that no fire suppression efforts have been made.  

## Modeling-Approach

To determine the locations that will be on fire the next day, we used 3 models:
1. Sklearn classifiers: Logistic Regression and Random Forest
2. TensorFlow: Convolutional Neural Network. 

Precision indicates the fraction of our fire predictions that are accurate whereas recall gives the fraction of the actual fires that are correctly predicted. The area under the precision–recall curve provides a more effective metric for imbalanced binary classification since the dataset mostly contained no fire.

The precision, recall, and area under the precision-recall curve rates of the models are given in the table below.

| Model | Precision | Recall  | AUC (PR) |
|:--------:|:-------:|:------:|:------:|
| Logistic Regression    | 38.48*   | 26.96	   | 19.45	   |
| Random Forest    | **39.72***  | 26.18	  | 20.67	  |
| Neural Network    | 32.20   | **46.28** | **29.27*** |

Table: * denotes the value is better than the baseline and the boldened values represent the overall best model.

The logistic regression and random forest models lacked spatial awareness of the fire spread. Like a feed forward neural network, they treat each 1x1 km sample as independent. For example, if wind is coming from the north, these algorithms have no information on if fire is north of you. They are unable to accurately predict whether the fire will spread to you or not. These models can work as a baseline, but they are not very helpful. 

Neural network uses spatial information along with 2.3 million parameters to predict whether each 1x1 km area will be on fire or not. This trained model can be evaluated on a new datapoint instantaneously, so as soon as the fire mask is created, first responders could run the model in minutes.

## Conclusions-and-Future-Directions

Wildfires are an inevitable part of our lives due to humans being the cause of 85% of fires. Predicting where the wildfire will spread helps rescue teams to distribute resources accordingly. Predicting wildfire spread  allows emergency rescue teams and citizens to properly prepare evacuation and relief strategies. 

In the future, these models could be applied to international data to predict wildfire spread in those regions. The models could be improved upon by accounting for existing fire suppression efforts. 

These wildfire spread prediction models could potentially save the United States hundreds of thousands of dollars in suppression efforts.

