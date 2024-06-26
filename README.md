# Tennessee Telehealth Analysis

## Project Description
I developed this notebook and presentation as part of my Capstone project for the Nashville Software School's Part-Time Data Analysis Bootcamp (Cohort DA11, Jan-Jun 2024). The presentation of the analysis can be found in the files. There are speaker notes available as well. 

## Adapting this Project
The project is tailored towards TN data but can easily be adapted to analyze the data for any state. Simply change all references to Tennessee in the code to the desired state (or national) designation. 

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

#### Part IV: Insights

#### Part V: References & Further Research


# Notebook Walkthrough:
## Part I: Data Questions & Sources
This project focuses on using data exploration and analysis to address the question: **``How can telehealth best be used to target health challenges in TN?``**  

Sub-questions include: 
1. What kinds usage of trends emerge in telehealth over the last few years, before and after covid?
2. Are there any demographic trends in telehealth usage?
3. Are there any correlations between health outcomes and factors that may be helpful in targeting telehealth initiatives?
4. What other challenges and considerations should be taken into account in implementing telehealth?

The data sources include two publiclly available datasets on telehealth trends among medicare and medicaid recipients respectively. There is also a robust dataset of various health factors and health outcomes collected in yearly datasets by the County Health Rankings & Roadmaps (CHR&R)  

##### 1. [Medicare Telehealth Trends](https://catalog.data.gov/dataset/medicare-telemedicine-snapshot) (medicare_trends_df)
 - The Medicare Telehealth Trends dataset provides information about people with Medicare who used telehealth services between January 1, 2020 and September 30, 2023.
 - The data was also used to generate the [Medicare Telehealth Trends Report](https://data.cms.gov/sites/default/files/2024-03/Medicare%20Telehealth%20Trends%20Snapshot%2020240307_508.pdf).
 - [Data Dictionary](https://data.cms.gov/sites/default/files/2023-05/c22a72d5-1b0c-47db-9d19-5fa75df96f4e/Medicare%20Telehealth%20Trends%20Report%20Data%20Dictionary_20220906_508.pdf) 

##### 2. [Medicaid & Chip Telehealth Trends](https://data.medicaid.gov/dataset/651fa253-4dd4-4867-8725-2b5ae1dd5ce9?conditions[0][property]=state&conditions[0][value]=Tennessee&conditions[0][operator]=%3D&conditions[1][property]=dataquality&conditions[1][value]=DQ&conditions[1][operator]=%3C%3E#data-table) (medicaid_trends_df)
 - This data set includes monthly counts and rates (per 1,000 beneficiaries) of services provided via telehealth, including live audio video, remote patient monitoring, store and forward, and other telehealth, to Medicaid and CHIP beneficiaries, by state. Data was collected between January 2018 and December 2022.
 - Note: Some states have serious data quality issues for one or more months, making the data unusable for calculating telehealth services measures...Cells with a value of “DQ” (in the DataQuality column). indicate that data were suppressed due to unusable data. Additionally, some cells have a value of “DS”. This indicates that data were suppressed for confidentiality reasons because the group included fewer than 11 beneficiaries.
 - There is no data dictionary for this data set

##### 3. [County Health Rankings and Roadmaps - TN](https://www.countyhealthrankings.org/health-data/tennessee?year=2023&measure=Mental+Health+Providers) (CHRR_df)
 - County Health Rankings & Roadmaps (CHR&R), a program of the University of Wisconsin Population Health Institute, is dedicated to understanding why there are differences in health within and across communities...CHR&R provides a snapshot of the health of nearly every county in the nation...
 - I looked only 2023 data (since 2024 is incomplete)
 - What Impacts Health? - See the [CHRR Health Model](https://www.countyhealthrankings.org/what-impacts-health/county-health-rankings-model)
 - [Data Dictionary](https://docs.google.com/spreadsheets/d/18rWeCagA0EANH2OibUtBEG1RMDMgdEL4drCRTT9ekWg/edit?gid=1203469383#gid=1203469383)

## Part II: Data Import & Cleaning
Read in the data for each source. Note that the CHRR dataset requires some preparation before import. The data set is large (many columns), so it might require filtering. In order to get workable data, I downloaded the CHRR "2023 Tennessee Data" [available here](https://www.countyhealthrankings.org/health-data/tennessee/data-and-resources) 

- I chose the 2023 data set because I want the most recent (but also complete) data set. 
- I then [opened the data in google sheets](https://docs.google.com/spreadsheets/d/1KXcrV9TsnZVKNOwzPdFz0CMRN-HucbbreOU3uATFW34/edit?gid=1441039258#gid=1441039258) 
- I separated out and saved the first sheet called "Introduction", which is basically the [data dictionary](https://docs.google.com/spreadsheets/d/18rWeCagA0EANH2OibUtBEG1RMDMgdEL4drCRTT9ekWg/edit?gid=1203469383#gid=1203469383)
- I combined the two sheets called "Ranked Measure Data" and "Additional Measure Data" into a new workbook
- I removed the top row bc there were merged columns
- I removed all the confidence interval columns since I won't be using them
- I also renamed the z-score columns so none have an identical name
- I kept only columns that may be related to my analysis 
- Finally I [downloaded the result](https://docs.google.com/spreadsheets/d/1HbkNcmNVu_r4-1v-McU214BBaMfUkkywQ251ns_FDjY/edit?gid=1115464274#gid=1115464274) into an excel sheet which I read in below (the download and the original are both also in the capstone data folder)

I could have also merged the two worksheets on FIPS, and dropped all the non-important columns (but using google sheets was faster in this instance)

The other two data sources did not require much tweaking. I left notes inside the code on my changes. 

