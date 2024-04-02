# Bellabeat_daily_activity_analysis
This analysis is focused on exploring Fitbit Fitness Tracker data to draw insights for business growth opportunities.

## TABLE OF CONTENT
[DELIVERABLES](#deliverables)  
[QUESTIONS TO ANSWER](#questions-to-answer)  
[DATA SOURCE](#data-source)  
[TOOLS](#tools)  
[DATA CLEANING/ PREPARATION](#data-cleaning-preparation)   
[DATA ANALYSIS](#data-analysis)   
[KEY FINDINGS](#key-findings)  
[RECOMMENDATIONS](recommendations)  
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
As you can see, the dataframe contains 16 columns, some of the columns have been aggregated.   
I only need 5 columns, so i will remove the unwanted columns and assing the resulting dataframe to a new vaiable  

```{R}
#remove all irrelevant columns
daily_activity <- daily_activity_merged %>%
  select(-'id',-'tracker_distance',-'logged_activities_distance',-'very_active_distance',-'moderately_active_distance',
         -'light_active_distance',-'moderately_active_distance',-'sedentary_active_distance', -'sedentary_minutes',
         -'very_active_distance',-'very_active_minutes',-'fairly_active_minutes',-'light_active_distance', -'lightly_active_minutes')
head(daily_activity)
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/e611285d-1744-4be7-b46c-e43fdca9f9c0)  

Now we are left with 5 relevant columns.  

Now we are going to delete all null rows, so our data can be as clean as possible.  

```{R}
#delete all null rows from the dataframe
daily_activity <- daily_activity %>%
  filter(!is.na('daily_activity')) %>%
  order_by(activity_date)
head(daily_activity)
view(daily_activity)
```
### DATA ANALYSIS
Next step is to check for the distribution of the fields in the data frame. This will enable us to examine the fields for skew in the data.  

```{R}
#check for the distribution of the columns
boxplot(daily_activity$calories, main="calories_boxplot",ylab="calories") 
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/57af7bac-8f1c-4eca-84de-a4c79267ec91)  

From our observation, there is a bit of a skew in the data caused by uneven distribution of the data.  
This is caused by days where there was signifacantly low calorie burn.  
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

As shown above, we expect a skew for total steps and total distance, because the users activity is not evenly distributed throughout the day.  

During the day, they are very active, and at night they are very less active.

To confirm this data distribution, we can look it up on a histogram chart.

```{R}
#carry out histogram plot to check their visual distribution
hist(daily_activity$calories)
hist(daily_activity$active_minutes)
hist(daily_activity$total_steps)
hist(daily_activity$total_distance)
```
The above chart gives a better visualization of the distribution of the field as explained above.

Since we are working with entries for 31 different users over the span of 31days, we are going to focus on the average of daily output for all users.
```{R}
#creating a new variable for the average(mean) of the columns for each day
avg_daily_activity <- daily_activity %>%
  group_by(activity_date) %>%
  summarize(avg_active_min = mean(active_minutes), avg_cal = mean(calories), avg_total_steps = mean(total_steps),
           avg_total_distance = mean(total_distance))
head(avg_daily_activity)
```
the above code generates the average of all fields per day for the 31 users.
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/49901622-0b61-47b4-934d-bcd920d45ac3)

since we generate the average of the fields per day, it is only fair to generate the sum of the fields too.
```{R}
#creating a new variable for the sum of the columns for each day
sum_daily_activity <- daily_activity %>%
  group_by(activity_date) %>%
  summarize(sum_active_min = sum(active_minutes), sum_cal = sum(calories), sum_total_steps = sum(total_steps),
      sum_total_distance = sum(total_distance))
```
At this point, We will use statistical analysis and plots to determine correllation and causation from the data.
the code below generates a plot for average calories burned per day for a period of 31 successive days. 
```{R}
# geom_line plot of the average calories over time in avg_daily_activity line plots  
ggplot(data = avg_daily_activity) +
  geom_line(mapping= aes(x=activity_date, y=avg_cal), color = "green", size= 1.5, fill=1) +
  geom_point(aes(x = activity_date, y = avg_cal), color = "red", size=2.0) +
  labs(title = "Average Calories Burned per Day")
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/8f881ba2-d138-4f27-a2a2-ea25d3207156)

The code below generates a plot for average total steps of all users combined, per day for a period of 31 successive days.
```{R}
# geom_line plot of the calories over time in avg_daily_activity line plots  
ggplot(data = avg_daily_activity) +
  geom_line(mapping= aes(x=activity_date, y=avg_total_steps), color = "red", size= 1.5) +
  geom_point(aes(x = activity_date, y = avg_total_steps), color = "blue", size=2.0) +
  labs(title = "Average Steps Walked per Day")
```  

![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/6beff1fd-548e-4494-82ef-389d4c2c02e3)

The code below generates a plot of the average active minutes of all users combined, per day for a period of 31 successive days.
```{R}
# geom_line plot of the calories over time in avg_daily_activity line plots  
ggplot(data = avg_daily_activity) +
  geom_line(mapping= aes(x=activity_date, y=avg_active_min), color = "brown", size= 1.5) +
  geom_point(aes(x = activity_date, y = avg_active_min), color = "yellow", size=2.0) +
  labs(title = "Average active minutes per day")
```  
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/00577541-a0e7-4db4-b3f3-8ad1b871aab6)

The code below generates a plot of the average distance covered by all users, per day for a period of 31 successive days.
```{R}
# geom_line plot of the calories over time in avg_daily_activity line plots  
ggplot(data = avg_daily_activity) +
  geom_line(mapping= aes(x=activity_date, y=avg_total_distance), color = "blue", size= 1.5) +
  geom_point(aes(x = activity_date, y = avg_total_distance), color = "green", size=2.0) +
  labs(title = "Average total distance walked per day")
```  
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/a59271e2-1d47-475b-8470-b13721bdc361)

clearly, al the plots look very similar. It's showing some correlation.  
Let's check for the correlation coefficient between each of these fields and "calories burned" to confirm the hunch.  
My primary focus is on calories burned.   

```{R}
cor(avg_daily_activity$avg_cal,avg_daily_activity$avg_total_distance)
cor(avg_daily_activity$avg_cal,avg_daily_activity$avg_active_min)
cor(avg_daily_activity$avg_total_steps,avg_daily_activity$avg_cal)
```
![Capture](https://github.com/pizzyander/bellabeat_wellness_R/assets/141561016/0898a87c-93e6-4d1e-8ef3-9cd35a7edb03)  
The above code shows that there is high correlation between each of these fields and "calories burned".  

### KEY FINDINGS  
The highest correlation is between average calories burned and the average total active minutes.
This shows that an increase in the activity of a user results has the highest influence on the calories burned by that user.
There is a reoccuring decrease in activity and calories burned on sundays.  

### RECOMMENDATIONS  
BellaBeat should make an upgrade to their app to include a feature that motivates the users to be more active.   
This would result in more calories burned and more satisfied users.  
BellaBeat can also insentivise their most active users to create an air of competition.

### LIMITATIONS  
The data provided does not span over a long period of time. This could have enabled me to view more trends in the data.
