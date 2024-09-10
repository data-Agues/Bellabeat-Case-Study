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

Sršen is conscious of the power of data-driven business decisions to help Bellabeat grow, so she's asked the marketing analysts team to analyse smart device data to gain insight into how consumers are using their smart devices. The findings will be shared to Bellabeat's key stakeholders (i.e. Sršen, Mur and Marketing team) in order to provide recommendations on how to apply marketing strategies to their own products based on the results derived.
The task will be achieved following Google's Data Analytics Professional Certificate journey of **Ask**, **Prepare**, **Process**, **Analyse**, **Share** and **Act** phases.

### Data:

- The dataset chosen for this study is [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit/data) uploaded by Möbius (you can check his profile [here](https://www.kaggle.com/arashnic)). It shows data from 30 "eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring".
- It is a CC0 Public Domain licensed post, so no permissions are needed to work with it.
- Data is mostly in long format, and was originally posted in Zenodo's [webpage](https://zenodo.org/) by Furberg, Robert; Brinton, Julia; Keating, Michael; Ortiz, Alexa. The link to the original dataset is [this](https://zenodo.org/record/53894#.YMoUpnVKiP9).
- The data's been checked using the ROCCC protocol:
  - Reliable: even though our sample size meets the CLT ≥ 30 criteria, a greater sample size is strongly recommended.
  - Original: the original datasource is located and the information comes from active users that agreed to share it.
  - Comprehensive: data is in raw format so no human error is found, although some points are missing because of each candidate's particular use of the device. 
  - Current: data is not current, since it was collected on 2016, so it shouldn't be used as a reference in the present period.
  - Cited: as stated before, the origin of the dataset is present and can be tracked for confidence.

For this study, I'll be using 3 different tools for the complete analysis and sharing process:

1. **_Spreadsheets_** for data _cleaning_ and _sorting_,
2. **_SQL_** for _analysis_ and _processing_,
3. **_Tableau_** for _visualizations_.

### Let's begin the exploration...

#### Process

As commented above, I'm going to use spreadsheets for the cleaning and sorting phase. At first sight, as expected, we find information about the users' activity, sleep, steps and calorie habits in a daily and hourly manner.

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
FROM `pristine-gadget-423406-i3.Fitbit_tracker_data.dailyActivity
`````
The query returned 33 participants:
```
