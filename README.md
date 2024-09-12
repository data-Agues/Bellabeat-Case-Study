# Bellabeat-Case-Study
## How can a tech-wellness company play it smart?

![bellabeat_logo](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTdw-ISja4k09NZu5Mn19WPYIis_oMk0G2suQ&s)

### About Bellabeat:
Bellabeat is a successful high-tech manufacturer of health-focused products for women with the potential to become a large player in the smart devices industry. Founded in 2013 by Urška Sršen and Sando Mur, their goal is to "develop beautifully designed technology that informs and inspires women around the world" through their different styled devices that track activity, sleep and stress:

- **Leaf** -> a wellness tracker that can be worn as a bracelet, necklace, or clip.
- **Time** -> a wellness watch that combines the timeless look of a classic timepiece with smart tracking technology.
- **Spring** -> a water bottle that tracks daily water intake using smart technology to ensure that you are appropriately hydrated throughout the day.

All of them come together in Bellabeat's *app*, that provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits to help them better understand their current habits and make healthy decisions.

### Business Task Summary:

Sršen is conscious of the power of data-driven business decisions to help Bellabeat grow, so she's asked the marketing analysts team to focus on one of their products and analyse smart device data to gain insight into how consumers are using their smart devices. The findings will be shared to Bellabeat's key stakeholders (i.e. Sršen, Mur and Marketing team) in order to provide recommendations on how to apply marketing strategies to their own products based on the results derived.
The task will be achieved following Google's Data Analytics Professional Certificate journey of **Ask**, **Prepare**, **Process**, **Analyse**, **Share** and **Act** phases.
The device chosen to focus on and apply the results to is the Leaf tracker. I made my choice based on the combination of style and usability combined in this particular piece.

### Data:

- The dataset chosen for this study is [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit/data) uploaded by Möbius (you can check his profile [here](https://www.kaggle.com/arashnic)). It shows data from 30 "eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring".
- It is a CC0 Public Domain licensed post, so no permissions are needed to work with it.
- Data is mostly in long format, and was originally posted in Zenodo's [webpage](https://zenodo.org/) by Furberg, Robert; Brinton, Julia; Keating, Michael; Ortiz, Alexa. The link to the original dataset is [this](https://zenodo.org/record/53894#.YMoUpnVKiP9).
- The data's been checked using the ROCCC protocol:
  - _Reliable_: even though our sample size meets the CLT ≥ 30 criteria (or does it?), a greater sample size is strongly recommended.
  - _Original_: the original datasource is located and the information comes from active users that agreed to share it.
  - _Comprehensive_: data is in raw format so no human error is found, although some points are missing because of each candidate's particular use of the device. However, no information is found about age, geographics, profession, etc., which will bring obvious limitations to the analysis later.
  - _Current_: data is not current, since it was collected on 2016, so it shouldn't be used as a reference in the present period.
  - _Cited_: as stated before, the origin of the dataset is present and can be tracked for confidence.

For this study, I'll be using 3 different tools for the complete analysis and sharing process:

1. **_Spreadsheets_** for data _cleaning_ and _sorting_,
2. **_SQL_** for _analysis_ and _processing_,
3. **_Tableau_** for _visualizations_.

### Let's begin the exploration...

#### Process

As commented above, I used spreadsheets for the cleaning and sorting phase. At first sight, as expected, we find information about the users' activity, sleep, steps and calorie habits in a daily and hourly manner.

Pre-processing techniques executed on each file include:

- Search for empty fields
- Duplicates removal
- Format consistency between dates and times
- ID length validation (=10 char)
- Splitted text to columns to separate dates and hours for a more in-depth analysis


#### Analyze

First, I wanted to check if the amount of participants was effectively 30:
```
SELECT
  COUNT(DISTINCT(Id)) AS total_participants
FROM
  `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyActivity
`````
The query returned 33 participants:

![image](https://github.com/user-attachments/assets/79934d32-c77c-485e-a5a0-47697f03e0c8)

Also, it is important to know the average use that the participants gave to the device, acknowledging the fact that it may run out of battery, they might not be wearing it or just not recording:
```
WITH Id_counts AS (
  SELECT
    Id,
    COUNT(*) AS appearance_count
  FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyActivity`
  GROUP BY
	Id
)
SELECT
  MIN(appearance_count) AS min_appearances,
  MAX(appearance_count) AS max_appearances,
  ROUND(AVG(appearance_count), 0) AS avg_appearances
FROM Id_counts;
```
![image](https://github.com/user-attachments/assets/2db7ef29-43a8-4c00-8430-8208eb286bf0)

That is a very wide range, and overall the average of days is less than the one expected of at least 30. This allows us to assume that there is a segmentation on how active users are with their devices. In any case, I found that in 24 cases (73%) there are logs for the entire period:
```
WITH Id_counts AS (
  SELECT
    Id,
    COUNT(*) AS appearance_count
  FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyActivity`
  GROUP BY
    Id
)
SELECT
  COUNT(Id) AS most_active_users
FROM Id_counts
WHERE
  Id_counts.appearance_count >= 30
```
![image](https://github.com/user-attachments/assets/10e54ffc-f734-47b4-9969-f58eea426fda)

I was also immediately interested in revising the total steps taken by each user, since this function seems to be very popular among fitness trackers:
```
SELECT
  ROUND(AVG(StepTotal)) AS avg_steps,
  MIN(StepTotal) AS min_steps,
  MAX(StepTotal) AS max_steps,
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailySteps`
```
![image](https://github.com/user-attachments/assets/8100bff7-0edf-49a2-8e2d-b55b729b91c9)

We once again find a very high range between the least and most active users, which reinforces the segmentation we referenced earlier. However, having a minimum of 0 steps as a the lowest value raises some doubts, since it looks almost imposible not to take a single step in a whole day period, or it may simply be due to the user not wearing it or having it out of battery, which would mean a NULL value.

Doing some reaserch, I found [this](https://www.thelancet.com/journals/lanpub/article/PIIS2468-2667(21)00302-9/fulltext#fig3) article from "The Lancet" that investigated "the effect of daily step count on all-cause mortality in adults". They found that, in women, from 8.000 to 12.000 steps in adults aged ≥ 40 and 7.500 steps in those aged ≥ 62, the risk of diseases or death significantly lowered. Our dataset does not include information about age, which is a critical limitation at this step, but is something worth noting later on in our recommendations to Bellabeat's team, that can use this numbers as a benchmark to warn their users that are, as we've seen, below it on average.

I next wanted to submerge into the Intensities table to check out how active our users are, and this is what I found:
```
SELECT
  ROUND(AVG(SedentaryMinutes)) AS avg_sedentary_minutes,
  ROUND(AVG(LightlyActiveMinutes)) AS avg_lightly_active_minutes,
  ROUND(AVG(FairlyActiveMinutes)) AS avg_fairly_active_minutes,
  ROUND(AVG(VeryActiveMinutes)) AS avg_very_active_minutes
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyIntensities`
```
![image](https://github.com/user-attachments/assets/ae952fbd-2b84-46db-89cd-d85e88dbecdf)

And as a % of the whole:
```
SELECT
  ROUND(SUM(SedentaryMinutes) / (SUM(SedentaryMinutes) + SUM(LightlyActiveMinutes) + SUM(FairlyActiveMinutes) + SUM(VeryActiveMinutes)), 2) * 100 AS sedentary_percentage,
  ROUND(SUM(LightlyActiveMinutes) / (SUM(SedentaryMinutes) + SUM(LightlyActiveMinutes) + SUM(FairlyActiveMinutes) + SUM(VeryActiveMinutes)), 2) * 100 AS lightly_active_percentage,
  ROUND(SUM(FairlyActiveMinutes) / (SUM(SedentaryMinutes) + SUM(LightlyActiveMinutes) + SUM(FairlyActiveMinutes) + SUM(VeryActiveMinutes)), 2) * 100 AS fairly_active_percentage,
  ROUND(SUM(VeryActiveMinutes) / (SUM(SedentaryMinutes) + SUM(LightlyActiveMinutes) + SUM(FairlyActiveMinutes) + SUM(VeryActiveMinutes)), 2) * 100 AS very_active_percentage
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyIntensities`
```
![image](https://github.com/user-attachments/assets/fc468029-9a21-4fdc-b2f6-7897527677b6)

Which shows us that MORE THAN 80% of the time our users are in sedentary intensity state. I think there is a clear chance to add value to the Bellabeat's app members to help them get moving by adding some training content directly to the app or by redirecting to some channel.

My next step was to find out at what time of the day were the most steps taken by the participants. Here's the top 5 most active hours of the day:
```
SELECT
  ActivityHour,
  SUM(StepTotal) AS sum_step_total,
  ROUND(AVG(StepTotal)) AS avg_steps_hour,
  MIN(StepTotal) AS min_step_total,
  MAX(StepTotal) AS max_step_total
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.hourlySteps`
GROUP BY
  ActivityHour
ORDER BY
  sum_step_total DESC
LIMIT 5
```
![image](https://github.com/user-attachments/assets/8ceac911-0321-4681-961c-143ef3232460)

And per day of the week as well, why not. But first, a "WeekDay" column needed to be created, since it was not originally included in the dataset. I achieved this through spreadsheets with the ```=TEXT()``` function.
```
SELECT
  WeekDay,
  ROUND(AVG(TotalSteps)) AS avg_steps
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyActivity_weekday`
GROUP BY
  WeekDay
ORDER BY
  avg_steps DESC;
```
![image](https://github.com/user-attachments/assets/785c4a0f-cffb-4c46-8aae-7fcb5bc72220)

It appears that Saturdays are the the most active days of the week on average.

Finally, I wanted to take a look at sleeping time, since it is a very important factor to consider when looking for a healthy lifestyle. [This](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6267703/) paper published by the official site of the National Center for Biotechnology Information, suggests getting 7-9 hours of sleep each day. Let's take a look at how are our participants comparing:
```
SELECT
  ROUND(AVG(TotalMinutesAsleep/60)) AS avg_sleep_hours,
  ROUND(MIN(TotalMinutesAsleep/60)) AS min_sleep_hours,
  ROUND(MAX(TotalMinutesAsleep/60)) AS max_sleep_hours
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailySleep`
```
![image](https://github.com/user-attachments/assets/815cf9c3-d430-4126-bba4-303a7a1df943)

Right on the lower boundarie, but inside the recommendations.

I also pretended to check the weight log info uploaded in the dataset, but after querying it found out only 8 participants out of the 33 had logged anything, so I discarded it for not being representative of the sample.

#### Share & Act

So in this last phase the idea is to share the insights and the findings of our work through visualizations, together with recommendations on how to act properly to take maximum advantage of the story the data is telling us.

As previously stated, the main goal is to assist Bellabeat's key stakeholders by planning a marketing strategy based on the results found focusing on one of their products, that in this case will be the Leaf Chakra, a wellness tracker designed as a piece of jewelry, made from hypoallergenic stainless steel and natural crystals. It tracks activity, menstrual cycles, sleep habits, and stress resistance. It can be worn as a bracelet, necklace, or clip.

![leaf](https://bellabeat.com/wp-content/uploads/2022/04/Untitled-2-3.jpg)

