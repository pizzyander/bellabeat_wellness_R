# Bellabeat_daily_activity_analysis
This analysis is focused on exploring Fitbit Fitness Tracker data to draw insights for business growth opportunities.

## TABLE OF CONTENT
[DELIVERABLES](#deliverables)  
[QUESTIONS TO ANSWER](#questions-to-answer)  
[DATA SOURCE](#data-source)  
[TOOLS](#tools)  
[DATA CLEANING/ PREPARATION](#data-cleaning-preparation)  
[EXPLORATORY DATA ANALYSIS](#exploratory-data-analysis)  
[DATA ANALYSIS](#data-analysis)  
[VISUALIZATION](#visualization)  
[KEY FINDINGS](#key-findings)  
[RECOMMENDATION](recommendation)  
[LIMITATIONS](limitations)  
### DELIVERABLES
1.	A clear summary of the business task 
2.	A description of all data sources used. 
3.	Documentation of any cleaning or manipulation of data 
4.	A summary of your analysis
5.	Supporting visualizations and key findings 
6.	Your top high-level content recommendations based on your analysis.

### QUESTIONS TO ANSWER
1.	What are some trends in smart device usage? 
2.	How could these trends apply to Bellabeat customers? 
3.	How could these trends help influence Bellabeat marketing strategy?

### DATA SOURCE
- Data source: Kaggle/ https://zenodo.org/record/53894#.X9oeh3Uzaao  
- Author: Arashnic Morbius.  
- About Data: BellaBeat Wellness (hourly_calories_merged, hourly_steps_merged, hourly_intensities_merged, daily_activity_merged)

### TOOLS
- Rstudio: Data cleaning  
- Rstudio: exploratory data analysis
- Rstudio: statistical analysis
- Rstudio: creating visualization

### DATA CLEANIN/ PREPARATION  
For a start, i installed all the necessary packages needed for the cleaning and analysis processes
```{R}
#install all necessary packages
install.packages("tidyverse")
install.packages("ggplot2")
install.packages("plotly")
library(plotly)
library(ggplot2)
library(tidyverse)
```
After that, i imported the dataset(csv file) into Rstudio from my computer
I viewed the dataset
```{R}
#check to view the dataset
View(daily_activity_merged)
head(daily_activity_merged)
```
![head dataframe](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/db80d1ec-2ee2-4f66-a1d3-b969e78dd1fe)
As you can see, the dataframe contains 16 columns, some of the columns have been aggregated. i only need 5 columns, so i will remove those unwanted columns
```{R}
#remove all irrelevant columns
daily_activity <- daily_activity_merged %>%
  select(-'id',-'tracker_distance',-'logged_activities_distance',-'very_active_distance',-'moderately_active_distance',
         -'light_active_distance',-'moderately_active_distance',-'sedentary_active_distance', -'sedentary_minutes',
         -'very_active_distance',-'very_active_minutes',-'fairly_active_minutes',-'light_active_distance', -'lightly_active_minutes')
head(daily_activity)
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/e611285d-1744-4be7-b46c-e43fdca9f9c0)
Now we are left with 5 relevant columns

Now we are going to delete all null rows, to our data is as clean as possible.
```{R}
#delete all null rows from the dataframe
daily_activity <- daily_activity %>%
  filter(!is.na('daily_activity')) %>%
  order_by(activity_date)
head(daily_activity)
view(daily_activity)
```
Next step is to check for the distribution of the fields in the data frame. This will enable us to examine the fields for skew in the data.
```{R}
#check for the distribution of the columns
boxplot(daily_activity$calories, main="calories_boxplot",ylab="calories") 
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/57af7bac-8f1c-4eca-84de-a4c79267ec91)
From our observation, there is a bit of a skew in the data caused by uneven distribution of the data. This is caused by days where there was signifacantly low calorie burn.  
But the data is still workable.  
Lets check the distribution for other fields  
```{R}
boxplot(daily_activity$active_minutes, main="active_minutes_boxplot",ylab="active_minutes")
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/0b3bc8c6-b7bd-4c2f-ae30-ec8a417dedf5)

```{R}
boxplot(daily_activity$total_steps, main="total_steps_boxplot",ylab="total_steps")
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/dc53f64a-e2b6-419d-a3d9-cafbf9988c00)

```{R}
boxplot(daily_activity$total_distance, main="total_distance_boxplot",ylab="total_distance")
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/e97b2f1a-0c81-47f9-b033-f8d1341b33d4)

