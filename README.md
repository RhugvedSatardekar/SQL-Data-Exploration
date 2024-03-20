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
