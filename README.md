<img width="364" alt="Screenshot 2024-03-12 at 6 44 31 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/dc7eeee1-ae19-44b0-a7cb-a58ca517771f"># Bellabeat-Data-Analysis-Case-Study

# Background
Bellabeat is a high-tech manufacturer of health-focused products for women, and meet different characters and team members.

As a data analyst, I was asked to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. The insights you discover will then help guide marketing strategy for the company.

# Ask
Below questions help me to better analysis.

What are some trends in smart device usage?
How could these trends apply to Bellabeat customers?
How could these trends help influence Bellabeat marketing strategy?

#### Business Task
To recommedation a suitable Bellabeat product throught analyze the wearable health deveice market needs and trends.

In order to achieve the business task, I am going to set some goals to analyze.

1. The relationship between intensity level and sleep quality
2. The relationship between device usage time and kg
3. The relationship between steps and calories consumed
4. The relationship between intensity level and BMI

# Prepare

#### Dateset

FitBit Fitness Tracker Data
Amazon

#### Data Sources
FitBit Fitness Tracker Data

FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): This Kaggle data set contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

Limitation： The FitBit is not updated since it was a 2016 data and its size is too small as it recorded 33 users' data only.

Azamon Sales Data

Azamon Sales Data: find the best sellers of top 5 products regarding smart bracelet, smart watch and smart water bottle.

Limitation：did not show the specific number in the best sellers

# Process
In this project, I will use R to process and analyze the dataset of FitBit Fitness Tracker Data.

In the FitBit Fitness Tracker Data, there are 18 tables and we need to find out the tables that provide the valuable insight so I need to import these data into R studio and check them one by one.

Install library for reviewing and cleaning the data.

install.packages("tidyverse")

Use the library

library("tidyverse")

Insert the csv files from FitBit Fitness Tracker Data into R Studio
```
#insert the data into R studio
dailyActivity <- read.csv("data/dailyActivity_merged.csv")
dailyCalories <- read.csv("data/dailyCalories_merged.csv")
dailyIntensities <- read.csv("data/dailyIntensities_merged.csv")
dailySteps <- read.csv("data/dailySteps_merged.csv")
heartrate_seconds_merged <- read.csv("data/heartrate_seconds_merged.csv")
hourlyCalories_merged <- read.csv("data/hourlyCalories_merged.csv")
hourlyIntensities_merged <- read.csv("data/hourlyIntensities_merged.csv")
hourlySteps_merged <- read.csv("data/hourlySteps_merged.csv")
minuteCaloriesNarrow_merged <- read.csv("data/minuteCaloriesNarrow_merged.csv")
minuteCaloriesWide_merged <- read.csv("data/minuteCaloriesWide_merged.csv")
minuteIntensitiesNarrow_merged <- read.csv("data/minuteIntensitiesNarrow_merged.csv")
minuteIntensitiesWide_merged <- read.csv("data/minuteIntensitiesWide_merged.csv")
minuteMETsNarrow_merged <- read.csv("data/minuteMETsNarrow_merged.csv")
minuteSleep_merged <- read.csv("data/minuteSleep_merged.csv")
minuteStepsNarrow_merged <- read.csv("data/minuteStepsNarrow_merged.csv")
minuteStepsWide_merged <- read.csv("data/minuteStepsWide_merged.csv")
sleepDay <- read.csv("data/sleepDay_merged.csv")
weightLogInfo <- read.csv("data/weightLogInfo_merged.csv")
```
I found dailyActivity, sleepDay_merged and weightLogInfo_merged are valuable for my analysis, so I removed the else by using rm().
```
#rm() example
rm(dailyCalories)
```
Data Cleaning
Now, I have 3 tables for my analysis

dailyActivity
sleepDay
weightLogInfo
I need to clean them before analyzing the data.

First, remove the duplicate items inside the tables by using distinct()
```
#remove the duplicate data

distinct(dailyActivity)
distinct(sleepDay)
distinct(weightLogInfo)
```
Second, remove the unnormal data in dailyActivity, calories should not be zero
```
#remove the data with calories is 0 in dailyActivity
dailyActivity <- dailyActivity[dailyActivity$Calories != 0, ]
```
Third, customize the columns for the analysis by using **mutate()**
```
#Table: dailyActivity
#add a column "Active_percentage" to represent the intensity level (higher is more intensive in a day)
#Active_percentage = Active_mins / Usage_mins
#Usage_mins = VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes + SedentaryMinutes
#Active_mins = VeryActiveMinutes + FairlyActiveMinutes
daily_activity <- daily_activity %>% mutate(Active_mins = VeryActiveMinutes + FairlyActiveMinutes) %>% mutate(Usage_mins = VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes + SedentaryMinutes) %>% mutate(Active_percentage = Active_mins / Usage_mins *100)
```
```
#Table: dailyActivity
#add a column "TotalAwakeTime" to represent the sleep quality (less means better sleep quality)
#TotalAwakeTime = TotalTimeInBed - TotalMinutesAsleep
sleep_day <- sleep_day %>% mutate(AwakeTime = TotalTimeInBed - TotalMinutesAsleep)
```

# Analyze

The cleaned data would be analyzed to understand the smart wearable device market and the needs of potential customers. Below are the 4 relationships to help us to get the insights from the data in terms of user activities, sleep and the weight.

#### Goals:

1. The relationship between intensity level and sleep quality
2. The relationship between device usage time and kg
3. The relationship between steps and calories consumed
4. The relationship between intensity levels and BMI
Based on the goals, we can see whether the hypothesises match actual relationships.

#### Hypothesises:

1. Higher intensity level has better sleep quality
2. Higher device usage time higher kg
3. Higher steps has higher calories consumed
4. Higher intensity level has normal BMI
   
#### Formatting Data

Unitifed all the datatime format to data

```
# DateTime to Date

sleep_day$SleepDay <- as.Date(sleep_day$SleepDay)
weightLogInfo$Date <- as.Date(weightLogInfo$Date)
```
Round up all the data to 2 decimal
```
# round up to 2 decimal

daily_activity <- daily_activity %>% mutate_all(~ round(., 2))
sleep_day <- sleep_day %>% mutate_all(~ round(., 2))
weightLogInfo <- weightLogInfo %>% mutate_all(~ round(., 2))
```
#### Analyzing Data

find the average data by each users.
```
# average data

avg_daily <- aggregate(cbind(steps, Usage_mins, Active_percentage, Calories) ~ Id, data = df, FUN = mean)
avg_sleep <- aggregate(cbind(AwakeTime) ~ Id, data = df, FUN = mean)
avg_weight <- aggregate(cbind(kg, BMI) ~ Id, data = df, FUN = mean)
```

#### Analyzing the relationship between intensity level and sleep quality
```
# first, combine the avg_daily and avg_sleep
merge_daily_sleep <- merge(avg_daily, avg_sleep, all = FALSE) %>% select(Id, Active_percentage, AwakeTime)
```
```
# second, compare the top 5 and bottom 5 intensive users to see the pattern

# top 5 
top_avg_active <- merge_daily_sleep %>% arrange(desc(Active_percentage)) %>% head(5) 
# bottom 5
bottom_avg_active <- merge_daily_sleep %>% arrange(Active_percentage) %>% head(5)
```

**top_avg_active**

<img width="370" alt="Screenshot 2024-03-12 at 6 42 52 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/6eedf5fc-b6dd-4573-a323-491c4a33ee3a">

**bottom_avg_active**

<img width="364" alt="Screenshot 2024-03-12 at 6 44 31 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/38359e14-4b65-4f46-92ca-1bb19aba5602">


There is no obvious relationship reflects in the data. Higher active time per day does not mean better sleep quality.

#### Analyzing the relationship between device usage time and kg

```
# first, combine the avg_daily and avg_weight
merge_usage_time_kg <- merge(avg_daily, avg_weight, all = FALSE) %>% select(Id, Usage_mins, Kg)
```
```
# second, compare the top 4 and bottom 4 device usage time users to see the pattern

# top 5 
top_avg_usage_time <- merge_usage_time_kg %>% arrange(desc(Usage_mins)) %>% head(4) 

# bottom 5
bottom_avg_usage_time <- merge_usage_time_kg %>% arrange(Usage_mins) %>% head(4)
```

**top_avg_usage_time**

<img width="292" alt="Screenshot 2024-03-12 at 7 25 18 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/b081d166-ab00-45ba-8a63-f2c48345f773">


**bottom_avg_usage_time**

<img width="289" alt="Screenshot 2024-03-12 at 7 25 28 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/07545ada-a4b1-4ff3-b82a-a44f1d94cfb6">


There is positive relationship between device usage time and kg. The users which have higher kg tend to use the wearable device for longer.

#### Analyzing the relationship between steps and calories consumed
```
# steps and calories from the same table, avg_daily, so the merged table is not required

# compare the top 5 and bottom 5 users' steps

# top 5 
top_steps_calories <- avg_daily %>% arrange(desc(steps)) %>% head(5) %>% select(Id, steps, Calories)

# bottom 5
bottom_steps_calories <- avg_daily %>% arrange(steps) %>% head(5) %>% select(Id, steps, Calories)
```

**top_steps_calories**

<img width="277" alt="Screenshot 2024-03-12 at 7 47 00 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/96c05f14-b145-4ac7-8096-154049ca8792">


**bottom_steps_calories**

<img width="276" alt="Screenshot 2024-03-12 at 7 48 24 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/8f8ea77d-11b7-4d77-88ba-2dadcc5431d6">


There is positive relationship between users' steps and calories consumed. The users which walk more tend to consume more calories.

#### Analyzing the relationship between intensity level and BMI

```
# first, combine the avg_daily and avg_weight
merge_daily_BMI <- merge(avg_daily, avg_weight, all = FALSE) %>% select(Id, Active_percentage, BMI)
```
```
# second, compare the top 4 and bottom 4 intensive users to see the pattern

# top 5 
top_avg_active_BMI <- merge_daily_BMI %>% arrange(desc(Active_percentage)) %>% head(4)

# bottom 5
bottom_avg_active_BMI <- merge_daily_BMI %>% arrange(Active_percentage) %>% head(4)
```

**top_avg_active_BMI**

<img width="325" alt="Screenshot 2024-03-12 at 8 46 18 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/1aacff4e-e934-4dc6-95be-695fb662b58e">


**bottom_avg_active_BMI**

<img width="324" alt="Screenshot 2024-03-12 at 8 46 34 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/d4dd61fb-b50c-492b-8816-20e883f4dc50">


Normal weight = 18.5–24.9 BMI

There is negative relationship between users' intensity level and BMI. The users which have higher intensity level tend to reach the normal weight BMI level.

#### Analyzing the Azamon Best Seller products regarding smart bracelet, smart watch and smart water bottle
##### top 2 smart bracelet

<img width="571" alt="Screenshot 2024-03-12 at 9 00 51 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/061956b3-71ea-495d-aabe-94a57af98b3a">

#### top 2 smart watch

<img width="1125" alt="Screenshot 2024-03-12 at 9 01 49 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/6ea612f1-9450-4c0f-a016-5ec50f3a346b">

#### top 2 smart water bottle

<img width="574" alt="Screenshot 2024-03-12 at 9 02 57 PM" src="https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/77bb10aa-8e78-4bb4-99fc-3a983232038a">

According to the search results from Amazon, top 2 smart bracelet have sold more than 800 in total, top 2 smart watch have sold more than 7000 in total and top 2 smart bracelet have sold more than 400 in total. Therefore, smart watch has been sold the most in those items.

# Share

Tableau would be the main tool for data visualization. The relationships would be visualized and provided the insights of the market needs. Before using Tableau, the processed data should be exported to the csv files.

```
# export the tables to csv format

write.csv(daily_activity, "daily_activity.csv", row.names = FALSE)
write.csv(sleep_day, "sleep_day.csv", row.names = FALSE)
write.csv(weightLogInfo, "weight_info.csv", row.names = FALSE)
```

#### The relationship between intensity level and sleep quality

![intensives_sleep](https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/57f9cea8-7df1-44f4-a7c2-4ece356fc0ea)

The above graph shows the intensity level has very less relationship with sleep quality. The majority users kept awake in the bed within 50 mintes, no matter with higher intensity level or lower intensity level. It represents there are other factors to have correlation with sleep quality.

#### The relationship between device usage time and kg

![usage_kg](https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/6f426f32-78a6-4114-9238-59f86681a00f)

The above graph shows that there is positive relationship between device usage time and kg. The users with more kg spent more time on the smart wearable devices. The users may want to lose weight by recording their their activity data, body level and planning fitness through the devices.

#### The relationship between steps and calories consumed

![steps_calories](https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/340eaa16-9782-40ac-ac85-96be791359fc)

The above graph shows the positive relationship between steps and calories consumed. The users walked more and their calories consumed was higher. walk may be an effective way for controlling calories.

#### The relationship between intensity level and BMI

![intensive_bmi](https://github.com/peterheung/Bellabeat-Data-Analysis-Case-Study/assets/163231245/8c7a6297-9989-44e7-a825-6ffa906f876c)

The above graph shows that there is slightly negative relationship between intensity level and BMI. The higher intensity level users tended to have relatively low BMI. However, normal BMI ranges from 18.5 to 24.9 and there are more higher intensity level users closed to the normal BMI level.

# Act

In the analysis, we have investigated the non-Bellabeat smart wearable products and the user usage situtaion from the market. We have analyzed the relationships in terms of intensity level, sleep quality, device usage time, kg, steps, calories consumed and BMI. Based on the analysises, we have a wide insight about the wearable device market and the potentail customers.

#### Findings
1. The intensity level related to the sleep quality in a slight level. There may be other factors to better understand the users' sleep qualities.

2. Positive association between device usage time and kg. The users with more weight tend to use the smart wearable devices. Therefore, the heavy 2eople may be our target customers.

3. Positive association between steps and calories burned. The users with more steps tend to hav more calories burned per day. Walk more may be an effective way of controlling weight.

4. The higher intensity level users tends to have the normal BMI. Increasing intensity level may help people to control their weight.

5. The smart watch is more popular than other smart fitness products in the market.

#### Recommedation
As smart watch has more sales than the smart bracelet and smart water bottle. It represents that people tend to choose smart watch for the fitness purpose. Therefore, our product, Time, will be the most suitable one for the potential customers. Moreover, we found that the heavier people tend to spend more time into the smart devices so our marketing strategies can focus on the heavy people, especially the weight is over 60 kg. Furthermore, we have found that more steps and higher intensity level per day is more effective for controlling weight. We can more focus on those functions in our App and the marketing promotion.

#### Next Step
Obviously, the data we used is not efficient for the complete analysis. There are only 33 user in the data and the data is not consistency. Some users even did not recorded their weight and sleep data. Also, we do not know the details of user information in the dataset from other company. Since our company mainly focus on the female customers, the dataset from fitbit may be misleading. Therefore, we can not have enough insights and we still need more our own data for the solid support. Not only the user size, we also can increase the measurements in different sections. For example, we can track the sleep quality in sleep stage, slight sleep and deep sleep. Therefore, we can provide more insights and the unique features for our customers to increase our competitive.

# Reference
BMI: https://www.nhlbi.nih.gov/health/educational/lose_wt/BMI/bmicalc.htm

Smart water bottle: https://www.amazon.com/s?k=smart+water+bottle&s=exact-aware-popularity-rank&crid=18CD6G3A6K403&qid=1710248521&sprefix=smart+water%2Caps%2C373&ref=sr_st_exact-aware-popularity-rank&ds=v1%3AS%2BCceFmOWYiWXuu8zexdh%2B0lZjcrcEvcxT8R0OSfYxM

Smart bracelet: https://www.amazon.com/s?k=smart+bracelet&crid=NN58QB52AMBT&sprefix=smart+bracel%2Caps%2C438&ref=nb_sb_ss_ts-doa-p_1_12

Smart watch: https://www.amazon.com/s?k=smart+wellness+watch&crid=3MOKFL2V2Q04X&sprefix=smart+well%2Caps%2C381&ref=nb_sb_ss_ts-doa-p_2_10

Sleep stage: https://blog.fitbit.com/sleep-stages-explained/
