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

--  Global death rate
SELECT SUM(new_cases) AS total_cases, SUM(cast(new_deaths AS INT)) AS total_deaths, SUM(cast(new_deaths AS INT))/SUM(new_cases)*100 AS death_rate
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent is not null

-- Top 10 countries with the highest death rate
SELECT location, population, MAX(CAST(total_deaths AS INT)) AS highest_death_count, 
(MAX(CAST(total_deaths AS INT))/population)*100 AS percent_population_death
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent IS NOT NULL
GROUP BY location, population
ORDER BY 4 DESC
LIMIT 10

-- Finding the case per population percentage. This is not the percentage of citizens that have tested positive in a country. Instead, this metric can be used to measure the transmission of Covid-19 in countries around the world and would include individuals who contract covid multiple times. Case_percentage should correlate with infection rate (shown below).

SELECT location, AVG(total_cases/population)*100 AS case_percentage
FROM `winged-record-348816.covid_project.covid-deaths`
GROUP BY location
ORDER BY case_percentage desc
LIMIT 10

SELECT location, population, MAX(total_cases) AS infection_count, AVG(total_cases/population)*100 AS case_percentage,
(MAX(total_cases)/population)*100 AS percent_population_infected
FROM `winged-record-348816.covid_project.covid-deaths`
WHERE continent IS NOT NULL
GROUP BY location, population
ORDER BY 5 DESC

-- Joining Covid-deaths and Covid_vaccinations
SELECT *
FROM `winged-record-348816.covid_project.covid-deaths` dea
JOIN winged-record-348816.covid_project.covid_vaccinations vac
	ON dea.location = vac.location
	AND dea.date = vac.date
  
-- Shows countries with the largest fully-vaccinated populations (assuming the average fully-vaccinated individual receives two shots)
SELECT dea.location, SUM(vac.new_vaccinations) AS total_vaccinations, ((SUM(vac.new_vaccinations)/2)/MAX(dea.population)*100) AS percent_toward_full
FROM `winged-record-348816.covid_project.covid-deaths` dea
JOIN winged-record-348816.covid_project.covid_vaccinations vac
	ON dea.location = vac.location
	AND dea.date = vac.date
  WHERE dea.continent IS NOT NULL AND vac.new_vaccinations IS NOT NULL
  GROUP BY dea.location
  ORDER BY 3 desc
  
-- Shows a rolling count of vaccines administered 
  SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, 
SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location
ORDER by dea.location, dea.date) AS rolling_count_vaccinations
FROM `winged-record-348816.covid_project.covid-deaths` dea
JOIN winged-record-348816.covid_project.covid_vaccinations vac
	ON dea.location = vac.location
	AND dea.date = vac.date
WHERE dea.continent IS NOT NULL AND vac.new_vaccinations IS NOT NULL
ORDER BY 2,3

-- Using CTE
WITH pop_vs_vac AS
(
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, 
SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER by dea.location, 
dea.date) AS rolling_count_vaccinations
FROM `winged-record-348816.covid_project.covid-deaths` dea
JOIN winged-record-348816.covid_project.covid_vaccinations vac
	ON dea.location = vac.location
	AND dea.date = vac.date
WHERE dea.continent IS NOT NULL AND vac.new_vaccinations IS NOT NULL
)
SELECT *, (rolling_count_vaccinations/population)*100 AS rolling_count_vac_percent
FROM pop_vs_vac

-- 
