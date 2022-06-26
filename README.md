# Covid Portfolio Project

## [View Code Here](https://github.com/jgreshy/Covid-Portfolio-Project/blob/main/code)

## Project Overview:
-- The data used in this project was collected from January 2020 to April 2021. The data comes from the World Health Organization and originally downloaded as two separate tables, one containing data about covid deaths and another containing data on covid vaccinations.
-- The focus of this project was to analyze the relationship between covid deaths and covid vaccinations as they were first introduced
## Data Structure
-- The data was configured in a way that created countries for each continent. These "countries" consolidated all data of all countried within the continent into reoccord. This happened because these "countries" continent attached to their record. Because of this, we'll be using 'WHERE continent IS NOT NULL' to exclude this repetitive data that would skew our results.
