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

Wildfires endanger lives, property, and the environment. Unfortunately, humans are the cause for approximately 85% of wildfires leading to thousands of deaths and billions of dollars worth of damage every year in the United States alone. By predicting where ongoing wildfire will spread, we can mitigate these significant costs  for stakeholders, like first responders and civilians, by optimally allocating resources to start evacuation and suppression efforts.

Using data of wildfires in the United States from 2012 to 2020 (Huot et al., 2022), the following questions can be answered:
* Which features are most important in wildfire spread prediction?
* How accurately can we predict where wildfires will spread?


## Data Collection

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

![My image](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/pictures/ViewDataset.png?raw=True)

The dataset also has an additional feature that includes the previous fire mask making the dataset have 18,545 fire events which are presented as 64 km x 64 km grids with 1 km resolution. We randomly crop a 32 km x 32 km square from each region for training, to reduce the computational complexity. We use the middle 32 km x 32 km square from each region for validation and testing. 

Only ~1% of the land in each 64 km x 64 km grid is on fire. Approximately 97% of the total dataset is not on fire. The remaining portion, more than 2% of our data is missing, usually due to smoke or cloud cover. This data is not used for training or evaluating the accuracy of the model.


## Exploratory Data Analysis

The most important features that predict wildfire spread in the **logistic regression model** are:


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


And in the **random forest model**:

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


## Predictability of Wildfire Spread

Predicting wildfire spread from this dataset is challenging because 97% of the data is _not_ on fire. Although we only care about the 1% of the data representing fire, we can trivially achieve a 97% accuracy score by predicting that nothing is on fire. Despite the high number, this data would be useless to first reponders or civilians, so we focus on Precision and Recall instead.
Again, it is trivial to manipulate these numbers to appear better than they are (e.g. predicting everything is on fire to achieve a 100% recall), so we report the precision and recall that maximize the F1-score, along with the area under the precision/recall curve.

Predicting wildfire spread is an inherently challenging problem, because fire behaves according to both natural and unnatural controls. Our model attemps to account for natural controls (wind, vegetation, natural elevation-based firebreaks), but is otherwise completely unable to account for anthropogenic controls like dumping water or setting up man-made firebreaks. As such, the model inherently assumes that no fire suppression efforts are being made, which reduces performance considerably. 

## Modeling Approach

To determine the locations that will be on fire the next day, we used 3 models:
1. Sklearn classifiers: Logistic Regression and Random Forest
2. Keras: Convolutional Neural Network. 

Precision indicates the fraction of our fire predictions that are accurate whereas recall gives the fraction of the actual fires that are correctly predicted. The area under the precision–recall curve provides a more effective metric for imbalanced binary classification since the dataset mostly contained no fire.

The precision, recall, and area under the precision-recall curve rates of the models are given in the table below.

| Model | Precision | Recall  | AUC (PR) |
|:--------:|:-------:|:------:|:------:|
| Logistic Regression    | 38.48*   | 26.96	   | 19.45	   |
| Random Forest    | **39.72***  | 26.18	  | 20.67	  |
| Neural Network    | 32.20   | **46.28** | **29.27*** |

Table: * denotes the value is better than the baseline and the **boldened** values represent the overall best model.

The logistic regression and random forest models lacked spatial awareness of the fire spread. Like a feed forward neural network, they treat each 1x1 km sample as independent. For example, if wind is coming from the north, these algorithms have no information on if fire is north of you. They are unable to accurately predict whether the fire will spread to you or not. These models can work as a baseline, but they are not very helpful. 

The neural network was based on the [U-Net Architecture](https://arxiv.org/abs/1505.04597), which uses 14 convolutional layers consisting of 2.3 million paramters to take advantage of spatial information within each image to predict whether each 1x1 km area will be on fire or not. In the encoder, we double the number of convolutional filters in each layer every time we halve the resolution using MaxPooling. In the decoder, we do the opposite, halving the number of filters each time we upscale the image using Conv2DTranspose. We also use skip connections at each layer to preserve location information through the bottleneck. This results in the highest recall and AUC measured on the test set, although it tends to overpredict where fire will be.

![My image](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/pictures/UNET.png?raw=True)

Since the trained model can be evaluated on a new datapoint almost instantaneously, as soon as the fire mask is created, first responders could run the model in minutes, as soon as the environmental data can be aggregated from Google Earth Engine using the steps outlined [here](https://github.com/google-research/google-research/tree/master/simulation_research/next_day_wildfire_spread#data-export).

## Conclusions and Future Directions

Wildfires are an inevitable part of the lives of millions of Americans, resulting in thousands of deaths and billions of dollars in damage every year. Predicting where  wildfires will spread helps rescue teams to distribute resources accordingly. Predicting wildfire spread allows stakeholders to properly prepare evacuation and relief strategies.

Our models were only trained on wildfires in the United States. In the future, these models could be applied to international data to determine if they still able to  accurately predict wildfire spread in those regions. The models could be improved upon by accounting for existing fire suppression efforts.

These wildfire spread prediction models could potentially save the hundreds of lives and millions of dollars in suppression efforts by optimizing resource allocation and management. The models can decrease the time it takes for resources to be allotted, which will lead to a decrease in fire damage and deaths.

## Description of Repository
The repository is very simple: all the notebooks we used for visualizing, analyzing, and evaluating the data are in the _notebooks_ folder!

* [kaggle_next_day_wildfire_demo.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/kaggle_next_day_wildfire_demo.ipynb) - This is a Jupyter Notebook demonstrating how to load and parse the data set. It was copied directly from the authors' [GitHub page](https://github.com/google-research/google-research/tree/master/simulation_research/next_day_wildfire_spread) and tweaked slightly to remove bugs.
* [Wildfire_EDA.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/Wildfire_EDA.ipynb) - Contains exploratory data analysis, including finding outliers (nevative elevation/wind speed, absolute 0 temperature, etc.), and calculating properties of the data set (how much fire is there? how much data is missing?)

The next three notebooks contain the three models we used to predict fire spread: Logistic Regression, Random Forst, Convolutional Neural Network (U-Net). For each of them, they import the dataset from Google Drive (you will have to change the location to where your data is stored locally), run the commands from [kaggle_next_day_wildfire_demo.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/kaggle_next_day_wildfire_demo.ipynb) to parse and normalize the inputs, then train the model and output the relevant statistics. 

* [Logistic_Regression_Modeling.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/Logistic_Regression_Modeling.ipynb) - We use Logistic Regression with no penalty on the entire 14,979 fires in the training set. This takes several minutes to run! Only datapoints with labels "0: No fire" or "1: Fire" are used for training. "-1: No Data" is neither used for training nor inference, allowing us to calculate precision, recall, and Area Under Curve (PR).
* [Random_Forest_Modeling.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/Random_Forest_Modeling.ipynb) - Same as above, it imports and normalizes the data, then performs a Grid Search to find the optimal parameter set for modeling. We found that [Maximum Depth: 4, Number of trees: 200, Number of Samples: 20000] performs best. It then re-trains that best model and reports precision, recall, and AUC (PR).
* [UNET_Model.ipynb](https://github.com/dwgb93/TEI_WildfireSpread/blob/main/notebooks/UNET_Model.ipynb) - After importing and normalizing the data, it runs it through a 14 layer U-Net convolutional neural network with BinaryCrossentropy loss (0: No Fire/1: Fire) with the [Adam Optimizer](https://arxiv.org/abs/1412.6980). Because the labels also contain missing data (-1: No Data), the functions at the beginning return sample weights of 0 for those pixels, ensuring they don't corrupt the model (which only outputs values on the interval [0, 1]).


