# covid

-- This is a test to see if it saves
-- The data was configured in a way that countries called Asia and Europe were formed (for each continent). The "countries" consolidated all of the data for each country within the continent into one. For these "countries" the continent was Null. We'll be using WHERE continent IS NOT NULL to exclude this data that could skew our results.

SELECT *
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent IS NOT NULL
ORDER BY 3,4

SELECT location, date, total_cases, new_cases, total_deaths, population
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent IS NOT NULL
ORDER BY 1,2

-- Looking at total cases vs total deaths
-- Shows the likelihood of dying if you contract covid in your country

SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS death_percentage
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent IS NOT NULL
ORDER BY 1,2

SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS death_percentage
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE location like '%States%'
ORDER BY 1,2

-- Looking at total cases vs population
-- This shows percentage of the US covid tests that are positive

SELECT Location, date, total_cases, population, (total_cases/population)*100 as positive_percentage
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE location like '%States%'
ORDER BY 1,2

-- Shows the total number of deaths per continent 
SELECT location, MAX(total_deaths) as Total_death_count
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent is null
GROUP by location
ORDER BY total_death_count desc

-- Shows the average death rate from 2020 to 2021 by continent in a descending order.
SELECT continent, SUM(new_cases) AS total_cases, SUM(cast(new_deaths as int)) AS total_deaths, SUM(cast(new_deaths AS INT))/SUM(new_cases)*100 AS death_rate
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent is not null
GROUP BY continent
ORDER BY death_rate desc
