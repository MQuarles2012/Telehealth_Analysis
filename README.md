# Tennessee Telehealth Analysis

## Project Description
I developed this notebook and presentation as part of my Capstone project for the Nashville Software School's Part-Time Data Analysis Bootcamp (Cohort DA11, Jan-Jun 2024). The presentation of the analysis can be found in the files. 

## Adapting this Project
The project is tailored towards TN data but can easily be adapted to analyze the data for any state. Simply use the filter function I created in Part III to filter by a different state. 

## Liscence Information
It is fine to use this code or any data herein for any purpose, as long as you credit the sources.  

## Table of Contents
#### Part I: Data Question & Sources
1. Data Question
2. Data Sources 

#### Part II: Data Import & Cleaning
1. Medicare Data
2. Medicaid & CHIP Data
3. TN County Health Rankings & Roadmaps

#### Part III: Data Exploration & Visualizations
1. Basic Aggregation Stats
2. Creating Scalable Filter Functions
3. Year over Year National Telehealth Trends (Medicare and Medicaid)
4. TN Medicare Demographic Trends in Telehealth Usage
5. TN Mental Health Trends (CHRR Data)

#### Part IV: Observations & Insights
1. Observations
2. Insights

#### References & Further Research

# Notebook Walkthrough:
## Part I: Data Questions & Sources

### 1. Data Questions
This project focuses on using data exploration and analysis to address the question: **``How can telehealth best be used to target health challenges in TN?``**  

Sub-questions include: 
1. What kinds usage of trends emerge in telehealth over the last few years, before and after covid?
2. Are there any demographic trends in telehealth usage?
3. Are there any correlations between health outcomes and factors that may be helpful in targeting telehealth initiatives?
4. What other challenges and considerations should be taken into account in implementing telehealth?

### 2. Data Sources
The data sources include two publiclly available datasets on telehealth trends among medicare and medicaid recipients respectively. There is also a robust dataset of various health factors and health outcomes collected in yearly datasets by the County Health Rankings & Roadmaps (CHR&R)  

##### 1. [Medicare Telehealth Trends](https://catalog.data.gov/dataset/medicare-telemedicine-snapshot) (medicare_trends_df)
 - The Medicare Telehealth Trends dataset provides information about people with Medicare who used telehealth services between January 1, 2020 and September 30, 2023.
 - The data was also used to generate the [Medicare Telehealth Trends Report](https://data.cms.gov/sites/default/files/2024-03/Medicare%20Telehealth%20Trends%20Snapshot%2020240307_508.pdf).
 - [Data Dictionary](https://data.cms.gov/sites/default/files/2023-05/c22a72d5-1b0c-47db-9d19-5fa75df96f4e/Medicare%20Telehealth%20Trends%20Report%20Data%20Dictionary_20220906_508.pdf) 

##### 2. [Medicaid & Chip Telehealth Trends](https://data.medicaid.gov/dataset/651fa253-4dd4-4867-8725-2b5ae1dd5ce9?conditions[0][property]=state&conditions[0][value]=Tennessee&conditions[0][operator]=%3D&conditions[1][property]=dataquality&conditions[1][value]=DQ&conditions[1][operator]=%3C%3E#data-table) (medicaid_trends_df)
 - This data set includes monthly counts and rates (per 1,000 beneficiaries) of services provided via telehealth, including live audio video, remote patient monitoring, store and forward, and other telehealth, to Medicaid and CHIP beneficiaries, by state. Data was collected between January 2018 and December 2022.
 - Important Note: Some states have serious data quality issues for one or more months, making the data unusable for calculating telehealth services measures...Cells with a value of “DQ” (in the DataQuality column). indicate that data were suppressed due to unusable data. Additionally, some cells have a value of “DS”. This indicates that data were suppressed for confidentiality reasons because the group included fewer than 11 beneficiaries.
 - There is no data dictionary for this data set

##### 3. [County Health Rankings and Roadmaps - TN](https://www.countyhealthrankings.org/health-data/tennessee?year=2023&measure=Mental+Health+Providers) (CHRR_df)
 - County Health Rankings & Roadmaps (CHR&R), a program of the University of Wisconsin Population Health Institute, is dedicated to understanding why there are differences in health within and across communities...CHR&R provides a snapshot of the health of nearly every county in the nation...
 - I looked only 2023 data (since 2024 is incomplete)
 - What Impacts Health? - See the [CHRR Health Model](https://www.countyhealthrankings.org/what-impacts-health/county-health-rankings-model)
 - [Data Dictionary](https://docs.google.com/spreadsheets/d/18rWeCagA0EANH2OibUtBEG1RMDMgdEL4drCRTT9ekWg/edit?gid=1203469383#gid=1203469383)

## Part II: Data Import & Cleaning
Either get the files from the original source (see links above) or use the files in the capstone-data folder on this repository. Then, read in the data for each source. I made the following alterations to each data set: 

### 1. Medicare Data
I renamed some of the columns for easier reference, but this is not required. I also multiplied and rounded the Pct_Telehealth column for easier readibility. Finally, I added a new column called "year-quarter" that combined year and quarter, for user in a later visualization. 

### 2. Medicaid & CHIP Data
There were some unusable data (described in notes from the dataset's origin website). I changed the service counts to a summable data type, removed commas, and converted some columns to numeric data types (coercing non-numbers to NaN). 

### 3. TN County Health Rankings & Roadmaps
The CHRR dataset requires some preparation before import. The data set is large (many columns), so it might require filtering. In order to get workable data, I downloaded the CHRR "2023 Tennessee Data" [available here](https://www.countyhealthrankings.org/health-data/tennessee/data-and-resources). I then applied this method: 
- I chose the 2023 data set because I want the most recent (but also complete) data set. 
- I then [opened the data in google sheets](https://docs.google.com/spreadsheets/d/1KXcrV9TsnZVKNOwzPdFz0CMRN-HucbbreOU3uATFW34/edit?gid=1441039258#gid=1441039258) 
- I separated out and saved the first sheet called "Introduction", which is basically the [data dictionary](https://docs.google.com/spreadsheets/d/18rWeCagA0EANH2OibUtBEG1RMDMgdEL4drCRTT9ekWg/edit?gid=1203469383#gid=1203469383)
- I combined the two sheets called "Ranked Measure Data" and "Additional Measure Data" into a new workbook
- I removed the top row bc there were merged columns
- I removed all the confidence interval columns since I won't be using them
- I also renamed the z-score columns so none have an identical name
- I kept only columns that may be related to my analysis 
- Finally I [downloaded the result](https://docs.google.com/spreadsheets/d/1HbkNcmNVu_r4-1v-McU214BBaMfUkkywQ251ns_FDjY/edit?gid=1115464274#gid=1115464274) into an excel sheet which I read in below (the download and the original are both also in the capstone data folder)

I could have also merged the two worksheets on the FIPS column, and dropped all the non-important columns, but using google sheets was faster in this instance. 

## Part III: Data Exploration & Visualizations

### 1. Basic Aggregation Stats
General first look at min, max, mean, and other common aggregates as applied to the medicare and medicaid data sets. 

### 2. Creating a Scalable Filter Function
The medicare data set had a large number of variables. It was easier to manage if approached via filter. I created a custom filter function called quickfilter(), designed to filter any dataframe by specific value(s) in a column. It takes three arguments: the name of the dataframe, the column to filter on (I defined all these as variables so I can just reference a shorter name), and the value or values to filter on. In this case I created a variable called "value" so I can change it easier. 

The result is a format that looks like this: 

``value = 'Tennessee' ``

``filtered_data = quick_filter(medicare_trends_df,state,value)``

** Best use case for this is to change the state:** 
If you want to filter by a specific state, change the "value" variable to the state you want, then run the quickfilter function. Once filtered, copy/paste as needed into the visualizations

notes: 
 - this function doesnt work when needing to filter by operators (like !=, <, >, etc.)
 - you can filter by other columns besides state
 - you can define multiple variables as the "value" (for example, Tennessee, Alabama, Georgia would include data from all three states). This could be helpful for regional analysis. 


### 3. Year over Year National Telehealth Trends (Medicare and Medicaid)
Includes two national trend charts: 
1. Medicare Telehealth Usage Year over Year - Quick visualization of overall telehealth trends across the data set (National) from 2020-2023
2. Medicaid Service Type Usage Year over Year - Looking at number of users by telehealth type between 2018-2022

### 4. TN Medicare Demographic Trends in Telehealth Usage
Medicare telehealth usage trends are broken down by:
- Race
- Age
- Gender
- Eligibility
- Rural vs. Urban

In the final presentation there are three charts for each demographic category above: 
1. Pie chart showing total enrollees as a percentage of whole within each category
2. Bar chart showing AVERAGE percentage for each demographic that used available telehealth services
3. Violin chart showing the DISTRIBUTION of the percentages for each demographic that used available telehealth services

### 5. TN Mental Health Trends (CHRR Data)
The County Health Rankings & Roadmaps (CHRR) is a national, publicly free data set that keeps track of a large variety of health factors and outcomes broken down by county in every state. I looked at 2023 TN data only. My analysis method included the creation of a correlation matrix, from which I then extracted statistically significant correlations, which I defined as either positive or negative 0.6 or higher. I then used Excel conditional formatting to highlight the significant variables and analyzed their relationships in a pivot table before charting them in python. I also included in the notebook an outline of the correlated variables. I then experimented with correlograms to give a quick view of which data sticks out among related variables. Based on these I chose a few variables to create individual scatter plots for the presentation. 

I ended up focusing on: 
 - [Mental Health Provider Rate](https://www.countyhealthrankings.org/health-data/health-factors/clinical-care/access-to-care/mental-health-providers?year=2024) - Number of mental health providers per 100k population 
 - [Average Number of Mentally Unhealthy Days](https://www.countyhealthrankings.org/health-data/health-outcomes/quality-of-life/poor-mental-health-days?year=2024)) - As reported in the past 30 days 
 - [% Frequent Mental Distress](https://www.countyhealthrankings.org/health-data/health-outcomes/quality-of-life/frequent-mental-distress?year=2024) - Percentage of adults reporting 14 or more days of poor mental health per month
 - [% Disconnected Youth](https://www.countyhealthrankings.org/health-data/health-factors/social-economic-factors/education/disconnected-youth?year=2024) - Percentage of young people between the ages of 16 and 24 who are not in school and not working
 - [% Rural](https://www.countyhealthrankings.org/health-data/demographics/rural?year=2024) I cross referenced the other variables with this one spot any trends relevant to the rural lens of my analysis

## Part IV: Observations & Insights
### 1. Observations
The notebook includes a detailed write-up of my observations for each data set and its visualizations. Much of this is reflected in the presentation and this portion is meant to be read alongside the charts and supplemental data included in the slides. 

### 2. Insights
Again, these are detailed in the notebook and presented in the slides. The general information is outlined below: 

#### 1. Targeting Mental Telehealth Initiatives
 - Telehealth initiatives in TN should be targeted towards engaging rural youth with mental health resources.
 - Minorities should be a priority, especially black youths who have a higher rate of displacement from work and school compared to their peers.
 - Telemedicine is not a replacement for in-person mental health treatment. Rather it is a good candidate for adding flexibility, continuity, and shoring up existing support-systems. 

#### 2. Challenges (and Solutions)
- Broadband Access: Broadband access is a well-known blocker to healthcare availability. A possible solution to this may be promoting methods of telehealth that do not require synchronous internet access. Specifically there is potential in using remote monitoring paired with store & forward methods of transmission to address lack of broadband access. 
- State Medicare & Medicaid Policies: Unfortunately TN is one of the only states that does not reimburse Store and Forward or Remote Patient Monitoring for Medicaid or Medicare beneficiaries. Anyone looking to build out telehealth in TN should consider raising awareness of this issue to policy makers and legislators.
- Technical Literacy: On this topic we should emphasize not only patient literacy, but also providers. Telehealth expansion programs could help providers improve their online presence and provide infrastructure for online portal scheduling. They could also help build out and promote mobile health apps for patients and providers to manage their health records and communications. 

## References & Further Research
The end of the notebook includes some references and articles/resources for further research
