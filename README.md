# Heart Disease Data Mining Project

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Gathering and Integration](#data-gathering-and-integration)
- [Data Cleaning and Preprocessing](#data-cleaning-and-preprocessing)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Predictive Analysis](#predictive-analysis)
- [Prescriptive Analysis and Optimization Model](#prescriptive-analysis-and-optimization-model)
- [Results and Findings](#results-and-findings)
- [Proposed Solution and Business Model](#proposed-solution-and-business-model)
- [Limitations](#limitations)
- [References](#references)

### Project Overview
---
This project involved an analysis of a heart disease dataset sourced from the UCI Machine Learning Repository. The primary goal was to conduct thorough exploratory data analysis and prepare the data for subsequent clustering and classification methods.

### Data Sources
---
The file downloaded from UCI describes the contents of the heart-disease directory.
Text from file:
  This directory contains 4 databases concerning heart disease diagnosis.
  All attributes are numeric-valued.  The data was collected from the
  four following locations:
  
      1. Cleveland Clinic Foundation (cleveland.data)
      2. Hungarian Institute of Cardiology, Budapest (hungarian.data)
      3. V.A. Medical Center, Long Beach, CA (long-beach-va.data)
      4. University Hospital, Zurich, Switzerland (switzerland.data)

  Each database has the same instance format.  While the databases have 76
  raw attributes, only 14 of them are actually used.  Thus I've taken the
  liberty of making 2 copies of each database: one with all the attributes
  and 1 with the 14 attributes actually used in past experiments.

  The authors of the databases have requested:

      ...that any publications resulting from the use of the data include the 
      #names of the principal investigator responsible for the data collection
      #at each institution.  They would be:

       1. Hungarian Institute of Cardiology. Budapest: Andras Janosi, M.D.
       2. University Hospital, Zurich, Switzerland: William Steinbrunn, M.D.
       3. University Hospital, Basel, Switzerland: Matthias Pfisterer, M.D.
       4. V.A. Medical Center, Long Beach and Cleveland Clinic Foundation:
	         #Robert Detrano, M.D., Ph.D.

Data Description:
- age: age in years
- sex: sex (1 = male; 0 = female)
- cp: chest pain type (Value 1: typical angina, Value 2: atypical angina, Value 3: non-anginal pain, Value 4: asymptomatic)
- trestbps: resting blood pressure (in mm Hg on admission to the hospital)
- chol: serum cholestoral in mg/dl
- fbs: (fasting blood sugar > 120 mg/dl)  (1 = true; 0 = false)
- restecg: resting electrocardiographic results (Value 0: normal, Value 1: having ST-T wave abnormality (T wave inversions and/or ST elevation or depression of > 0.05 mV), Value 2: showing probable or definite left ventricular hypertrophy by Estes' criteria)
- thalach: maximum heart rate achieved
- exang: exercise induced angina (1 = yes; 0 = no)
- oldpeak: ST depression induced by exercise relative to rest
- slope: the slope of the peak exercise ST segment (Value 1: upsloping, Value 2: flat, Value 3: downsloping)
- ca: number of major vessels (0-3) colored by flourosopy
- thal: 3 = normal; 6 = fixed defect; 7 = reversable defect
- num: diagnosis of heart disease (angiographic disease status) (Value 0: < 50% diameter narrowing, Value 1: > 50% diameter narrowing)

### Tools
---
- RStudio - Data Cleaning and Preprocessing, Data Analysis (EDA, Descriptive Analysis), Machine Learning (Clustering, Classification)

### Data Gathering and Integration
---
In the initial data gathering and integration phase, I performed the following tasks:
1. Data loading and inspection.
2. Adding column names.
3. Constructing a new variable, 'disease'.
4. Converting variables to be categorical or numeric.

### Exploratory Data Analysis
---
EDA involved exploring the heart disease data to consider the distributions of each variable and at least some of the relationships between pairs of variables.

Examples of code worked with

```R
# age
summary(heart$age)
# To find bin width: (Max(x)-Min(x))/Number of Bins = Bin Width
# (77.00-29.00)/8 ≈ 6
# Histogram of age
ggplot(heart, aes(x = age)) + 
  geom_histogram(binwidth = 6, fill = "red", alpha = 0.7, color = "black") +
  labs(title = "Distribution of Age", x = "Age", y = "Count") +
  theme_minimal()
# The histogram for 'age' shows a slight negative skew, with values slightly concentrated on the right. The skewness of the distribution of 'age' is also backed up by the fact that the mean value is less than the median.

# disease (target variable)
ggplot(heart, aes(x = disease)) +
  geom_bar(fill = "red", alpha = 0.7, color = "black") +
  labs(title = "Bar Graph of disease", x = "disease", y = "Count")
table(heart$disease)
# The bar graph of 'disease' shows that the data is distributed fairly evenly between values 0 and 1, no diagnosis of disease or diagnosis of disease. A table of 'disease' further confirms this and shows us the counts for each value; 0 = 164 and 1 = 139. This shows us that the data set is fairly split between this variable, with 54.1% of individuals in the data having no diagnosis of heart disease and 45.9% of individuals having a diagnosis of heart disease. 
```
```R
## Exploring some of the relationships between pairs of variables
# Bar Graph of cp vs. disease
ggplot(heart, aes(x = cp, fill = disease)) +
  geom_bar(position = "dodge") +
  labs(title = "Chest Pain Type vs. Heart Disease", x = "Chest Pain Type", y = "Count", fill = "Heart Disease") +
  theme_minimal()

# This bar graph raises a very interesting finding, where the majority of individuals with heart disease have a chest pain value of 4, meaning asymptomatic (no chest pain reported). In the other types of chest pain, where a variation of chest pain is reported, individuals with no heart disease heavily outweigh those that do have disease.
```

![Rplot1](https://github.com/user-attachments/assets/bb9c2479-e576-4793-91cf-91ee500112c5)

```R
# Bar Graph of age vs. disease
ggplot(heart, aes(x = age, fill = disease)) +
  geom_bar(position = "dodge") +
  labs(title = "Age vs. Heart Disease", x = "Age", y = "Count", fill = "Heart Disease") +
  theme_minimal()
# This bar graph shows large counts of individuals without heart disease younger than 55 years old, but shows a large sum of individuals with heart disease older than 55 years old. Thus, age seems to play a factor in whether or not an individual is diagnosed with heart disease.
```

![Rplot2](https://github.com/user-attachments/assets/11cc3284-f00d-4dc5-8154-3782b50f676b)

### Data Cleaning and Preprocessing
---
In the data cleaning and preprocessing phase, I performed the following tasks:
1. Verifying if the data set contains unique observations.
2. Finding and addressing missing values (NA's) in the data set.
3. Applying a log transformation to a variable.
4. Creating dummy variables.
5. Standardizing the data.

### Machine Learning Methods
---
Clustering Analysis

This section of the analysis explores the application of clustering techniques to the heart disease dataset to identify potential groupings within the data.

Two primary methods were employed: K-means clustering and Hierarchical Agglomerative Clustering (HAC).

1. K-Means Clustering
- Libraries: The `stats` and `factoextra` libraries were loaded. `factoextra` was used to help visualize and determine the optimal number of clusters.
- Preprocessing: The preprocessed predictor variables were used as input for the K-means algorithm.
- Optimal K Determination: The optimal number of clusters (K) was determined using two methods: The "elbow" method, visualized with `fviz_nbclust` using the "wss" (within-cluster sum of squares) method, suggested K=2 as the point where the rate of decrease in WSS diminishes. The silhouette method, also visualized with `fviz_nbclust`, indicated that K=2 had a very similar average silhouette width to K=3, and given the result of the elbow method, K=2 was chosen. Choosing a smaller K was done to create more distinct groupings.
- Clustering: The K-means algorithm (`kmeans`) was applied with K=2 and multiple restarts (`nstart = 25`) to ensure a robust solution.
- Visualization: The resulting clusters were visualized using `fviz_cluster`. Principal Component Analysis (PCA) was performed using `prcomp` to reduce the dimensionality of the predictor variables for visualization. The data was then projected onto the first two principal components (PC1 and PC2), and plotted using `ggplot2` to visualize clusters, colored by both the original "disease" labels and the assigned cluster labels. This visualization aided in comparing the clustering results with the actual disease status.
- Observation: The visualization showed a reasonable separation between clusters and some alignment with the actual disease status.

2. Hierarchical Agglomerative Clustering (HAC)

- Library: The `cluster` library was loaded to use the `daisy` function.
- Distance Matrix Calculation: The `daisy` function was used to calculate the Gower distance matrix, which is suitable for handling mixed data types (both categorical and numerical variables) present in the `heart_data`.
- Clustering: Hierarchical clustering was performed using `hclust` with the "average" linkage method.
- Dendrogram Visualization: The resulting dendrogram was plotted to visualize the hierarchical clustering structure.
- Optimal K Determination: Like K-means, the optimal number of clusters for HAC was determined using the elbow and silhouette methods, implemented with `fviz_nbclust` and the `hcut` function. Both methods suggested K=2.
- Cluster Assignment: Clusters were assigned by cutting the dendrogram at K=2 using `cutree`.

Comparison of Clustering Results:
- A crosstabulation was created using the `table` function to compare the cluster assignments from K-means and HAC. The comparison revealed a high degree of consistency between the two clustering methods, with only a small number of data points being assigned to different clusters by the two approaches.





### Prescriptive Analysis and Optimization Model
---
The predictions for demand between our five chosen stations were then input into the parameters of our optimization model. Due to the high daytime/evening demand of our selected stations, we had to change the number of bikes from 1,000 to 4,000 to satisfy demand.

Using this optimization model allowed us to find:
- How many bikes should we allocate to each station?
  - Based on our model and the number of bikes, the demand forecast showed that the best initial allocation was 3,427 bikes.
- How many trips between stations every day?
  - Given our parameters, the demand forecast showed the number of trips to be 6,244.

### Results and Findings
---
The analysis results are summarized as follows:
1. On average, evenings have a higher demand than mornings.
2. On average, demand slightly decreases during the weekend for both times of day.
3. Our multiple linear regression model explains 89.56% of the variance in our data.
4. Given our five stations, the best initial allocation of bikes is 3,427 bikes.

### Proposed Solution and Business Model
---
Based on the analysis, we recommend the following actions:
- Increase the initial allocation of bikes to 3,427.
- Provide cashback or discount opportunities to riders/people close to stations to displace extra bikes to the nearest high-demand stations.
- Allow commuters/students to earn extra money/reward for displacing bikes from one station to another suggested station (Ex. Earn a reward per 5 bikes displaced).
- A designated app for Citi Bike could manage and display all the displacement suggestions, discounts, cashback, and student account information.

### Limitations
---
All null values were removed from the dataset, which only took out a small percentage of the data. This was to ensure null values were not being used in the analysis and also so the multiple linear regression model would work.

### References
---
1. [Data](https://archive.ics.uci.edu/dataset/45/heart+disease)
