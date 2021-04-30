# Bellabeats Case Study

The co-founder and Chief Creative Officer, Urška Sršen has asked that I analyze FitBit product usage in order to find trends in usage.  This information will be used to identify growth opprotunites and will result in my high level recommendations for marketing strategy.

## The Data

The data can be found [here.](https://www.kaggle.com/arashnic/fitbit)  This is a public dataset on Kaggle that contains daily, hourly, and minute data from 33 users over a 31 day timeframe. The data is broken up into 18 tables containing: steps, distance, caloric, and sleep data.  

I chose to focus soley on the daily intervals as the minute and hourly data is likely too granular for this usecase.  These tables are: 'dailyCalories_merged.csv', 'dailyActivity_merged.csv', 'dailyIntensities_merged.csv', and 'dailySteps_merged.csv'.  

## Data Cleaning and Manipulation

1. Created 'FitBit_Data' dataset in Google BigQuery.

2. Uploaded 'dailyCalories_merged.csv', 'dailyActivity_merged.csv', 'dailyIntensities_merged.csv',
	'dailySteps_merged.csv' to 'FitBit_Data' in BigQuery.

3. BigQuery could not upload 'dailySleep_merged.csv' due to a formatting error.  So I cleaned 'dailySleep_merged.csv' with Excel; seperated date and time into seperate columns; converted time to 24hr.  Then uploaded to 'FitBit_Data'.

4. Created table 'allMetrics' by performing various JOINS, ON 'Id' and 'ActivityDate' with the following query:

```SQL
SELECT*

FROM FitBit_Data.dailyActivity 

    JOIN FitBit_Data.dailyCalories ON dailyActivity.Id=dailyCalories.Id 
    AND dailyActivity.ActivityDate=dailyCalories.ActivityDay

    JOIN FitBit_Data.dailyIntensities ON dailyActivity.Id=dailyIntensities.Id 
    AND dailyActivity.ActivityDate=dailyIntensities.ActivityDay

    JOIN FitBit_Data.dailySteps ON dailyActivity.Id=dailySteps.Id 
    AND dailyActivity.ActivityDate=dailySteps.ActivityDay

    LEFT JOIN FitBit_Data.dailySleep ON dailyActivity.Id=dailySleep.Id
    AND dailyActivity.ActivityDate=dailySleep.SleepDay

ORDER BY dailyActivity.Id, ActivityDate
```

5. Created table 'cleanedMetrics' by removing duplicated columns from 'allMetrics' with the following query:

```SQL
SELECT* 
    EXCEPT (Id_1, 
    Id_2,
    Id_3,
    Id_4,
    ActivityDay,
    ActivityDay_1,
    ActivityDay_2,
    SleepDay,
    TrackerDistance,
    StepTotal,
    Calories_1,
    SedentaryMinutes_1,
    LightlyActiveMinutes_1,
    FairlyActiveMinutes_1,
    VeryActiveMinutes_1,
    SedentaryActiveDistance_1,
    LightActiveDistance_1,
    ModeratelyActiveDistance_1,
    VeryActiveDistance_1)
FROM
    bellabeats-project.FitBit_Data.allMetrics

ORDER BY Id, ActivityDate DESC
```

6. Created table 'cleanedAverages' with averages of columns in'cleanedMetrics'.

```SQL
SELECT
    AVG(TotalSteps) AS Avg_Total_Steps,
    AVG(TotalDistance) AS Avg_Total_Distance,
    AVG(VeryActiveDiseftance) AS Avg_Very_Active_Distance,
    AVG(ModeratelyActiveDistance) AS Avg_Moderately_Active_Distance,
    AVG(LightActiveDistance) AS Avg_Light_Active_Distance,
    AVG(SedentaryActiveDistance) AS Avg_Sedentary_Active_Distance,
    AVG(VeryActiveMinutes) AS Avg_Very_Active_Minutes,
    AVG(FairlyActiveMinutes) AS Avg_Fairly_Active_Minutes,
    AVG(LightlyActiveMinutes) AS Avg_Lightly_Active_Minutes,
    AVG(SedentaryMinutes) AS Avg_Sedentary_Minutes,
    AVG(Calories) AS Avg_Calories,
FROM
    bellabeats-project.FitBit_Data.cleanedMetrics
```

## Analysis and Findings

### Summary Analysis

1. Sleep services are ripe with growth potential.  With less than half of users entering sleep data.  And less than half of users reporting achieving a full 8 hours of sleep.  This area is underutilized and can be grown.  
2. Users tend to wear their device everyday, and most users wear the device all day.  I believe this trend supports an premium upmarket product. Additonally thre might be room to collaborate with exisitng designers.  
4. Users average ~20 minutes of 'very active activity' daily vs. ~990 minutes of 'sedentary activity' daily (16.5 hrs).  This trend shows that users are not wearing the device soley during or even soley for fitness activity.  I believe this trend further supports the case for an upmarket product and opens up marketing messaging around the idea that "Sitting is the new smoking" and how we can help with this problem.  

### Full Analysis

#### Sleep Usage Trends

Of the 33 users in this sample only 16 provided sleep data.  Among the users who did create sleep records the most active respondents only created at most, 4 records with the average being 3.
<div class='tableauPlaceholder' id='viz1619814342454' style='position: relative'><noscript><a href='#'><img alt='Total Number of Sleep Records Created Per User ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet6&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BellabeatsProject&#47;Sheet6' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet6&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /></object></div> 

The lack of data here implies a lot of room for growth in usage.  I would recommend driving growth in usage of sleep features by offering to automatically detect when the user is sleeping.  FitBit is requiring that users manually create sleep records and this burden seems to cause a significant reduction in use of sleep services.  

Furthermore less than half of the users creating sleep data achieved 8 hours of sleep.
<div class='tableauPlaceholder' id='viz1619815329235' style='position: relative'><noscript><a href='#'><img alt='Average Minutes Asleep Per Day ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet7&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BellabeatsProject&#47;Sheet7' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet7&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /></object></div>

I recommend positioning ourselves as making it effortless to track sleep habits.  We should leverage the fact that most users are not achieving 8 hours of sleep nightly.  This strategy might draw users from FitBit who don't want to manually track their sleep habits and will make our products more sticky to existing Bellabeats users who don't want to manually track their sleep if considering switching brands.  

#### General Usage Trends

Over half of all users wear their device everyday.  Among the users who do not wear the device daily, only 3 users in our sample **did not** wear their device for more than 5 days (out of the 31 day period).  
<div class='tableauPlaceholder' id='viz1619816748090' style='position: relative'><noscript><a href='#'><img alt='Days User Has Used Device ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet3&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BellabeatsProject&#47;Sheet3' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet3&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /></object></div>

I believe that this trend would support an premium upmarket device.  Since users wear the device daily (I will show later that they also tend to wear the device **all day**) users would likely be receptive to a device that featured a more premium design and materials.  Imagine our current [flagship design](https://bellabeat.com/product/leaf/) with a gemstone, this product would have a very high profit margin as there will be a lot of room to markup the gemstone.  Additonally this could angle the marketing messaging that our device is so nice users won't want to take it off, resulting in the user being that much more accountable to their health and fitness goals.  

#### Types of Usage Trends

Users on average are engaging in ~20 minutes of "Very Active Minutes" per day.  
<div class='tableauPlaceholder' id='viz1619817894525' style='position: relative'><noscript><a href='#'><img alt='Users Average Daily &quot;Very Active Minutes&quot; ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet4&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BellabeatsProject&#47;Sheet4' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet4&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /></object></div>

Contrast this with the chart below showing that users on average engage in ~990 minutes (16.5 hrs) of sedentary activity daily.
<div class='tableauPlaceholder' id='viz1619818216572' style='position: relative'><noscript><a href='#'><img alt='Average Inensity of User&#39;s Daily Activity by Minute ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet2&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BellabeatsProject&#47;Sheet2' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Be&#47;BellabeatsProject&#47;Sheet2&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en' /></object></div>

The sedentary portion of this chart is in my view, a huge potential area for growth.  We have traditionally focused on the exercise and fitness aspect of our devices however I recommend that we focus on the time in between.  We should utilize messaging that "Sitting is the new smoking" and offer features that allow us to deliver alerts to users when they have, for example: spent "X amount of time in a sedentary state", or "It has been X amount of time since you were in X state".  We can compare different inensities with realtime data for that day, imagine how impactful a notification that "98% of your day has been in a sedentary state" (which is the real breakdown for the average user: 20 / 990 = 2%) could be to our users.  

A device that makes users more accountable to their fitness and health goals will lead to a more invested user.  This user would be more likely to invest in a premium version of their device.  

Lastly, since we know that most users don't take their device off, we might consider collaborating with established/popular designers to market unique verions of our existing wearables.  This would likely increase wear time (driving data uptake) and potentially bring in new customers.  
