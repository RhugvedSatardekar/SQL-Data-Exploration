# Covid Deaths Project SQL Summary

## Overview
This project involves analyzing and transforming data related to Covid cases, deaths, vaccinations, and population statistics from the CovidDeaths and CovidVaccinations tables.

## Data Selection and Analysis

1. **Selecting Data for Analysis**
   - Selected columns: Location, date, total_cases, new_cases, total_deaths, population from CovidDeaths table.

2. **Total Cases Vs. Total Deaths in USA**
   - Calculated death percentage for each state in the USA to show likelihood of dying if infected.

3. **Total Cases Vs. Population (Countries)**
   - Calculated total infection rate by dividing max total cases by population for each country.

4. **Highest Death Count per Population**
   - Identified countries and continents with highest death count per population and calculated death percentage.

5. **Global Numbers**
   - Calculated global stats: total cases, total deaths, and death percentage.

6. **Total Vaccinated People**
   - Joined CovidDeaths and CovidVaccinations tables to analyze total vaccinations and vaccination percentages.

7. **Rolling New Vaccinations**
   - Tracked cumulative count of new vaccinations over time for each location using rolling calculations.

8. **Total Percentage of New People Vaccinated**
   - Calculated percentage of new people vaccinated based on rolling vaccination counts and population data.

## View Creation

1. **Creating View for Visualization**
   - Created `PercentPopulationVaccinated` view to store data for later visualizations, including continent, location, date, population, new vaccinations, rolling people vaccinated, and vaccination percentage.

## Conclusion
This README summarizes the key SQL operations performed in the Covid Deaths Project, providing insights into data analysis, transformation, and visualization processes.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# SQL Queries for Covid Deaths Project

## Overview
This README contains SQL queries used for analyzing Covid-related data from the [Covid Deaths Project] database.

### 1. Selecting Data for Analysis
```sql
Select Location, date, total_cases, new_cases, total_deaths, population
From [Covid Deaths Project]..CovidDeaths
```

### 2. Total Cases Vs. Total Deaths in USA
```sql
Select Location, date, total_cases, total_deaths, round((total_deaths/total_cases) * 100,2) as Death_Percetage
From [Covid Deaths Project]..CovidDeaths
where Location like '%states%'
order by location, Death_Percetage
```

### 3. Total Cases Vs. Population (Countries)
```sql
select Location, max(total_cases) as total_cases, population, (Max(total_cases)/(population))*100 as Total_Infection_rate
from [Covid Deaths Project]..CovidDeaths
group by Location, population
Order by Total_Infection_rate
```

### 4. Countries with Highest Death Count per Population
```sql
select Location, max(cast(total_deaths as int)) Total_Deaths, population, round((max(cast(total_deaths as int))/population)*100,4) as DeathPercent
from [Covid Deaths Project]..CovidDeaths
where continent is not null
group by Location, population
order by DeathPercent Desc
```

### 5. Continents with Highest Death Count per Population
```sql
select Location, max(cast(total_deaths as int)) Total_Deaths, population, round((max(cast(total_deaths as int))/population)*100,4) as DeathPercent
from [Covid Deaths Project]..CovidDeaths
where continent is null
group by Location, population
order by DeathPercent Desc
```

### 6. Global Numbers
```sql
Select SUM(new_cases) as global_cases, SUM(cast(new_deaths as int)) as global_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From [Covid Deaths Project]..CovidDeaths
where continent is not null 
order by 1,2
```

### 7. Total Vaccinated People
```sql
select CD.location, CD.population, max(CV.people_vaccinated) Total_Vaccinations, round((max(CV.people_vaccinated)/CD.population)* 100 ,2) Vaccination_Percentage from [Covid Deaths Project]..CovidDeaths as CD
join [Covid Deaths Project]..CovidVaccinations as CV
on CD.location = CV.location and
CD.date = CV.date
where CD.continent is not null
group by CD.location, CD.population
Order by Vaccination_Percentage desc
```

---

The remaining queries are structured similarly with appropriate headings and code blocks to enhance readability and understanding for viewers. Adjustments can be made as per your specific requirements.
