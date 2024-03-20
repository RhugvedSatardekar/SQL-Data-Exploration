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


-- Selecting Data that we are going to be analyse

Select Location, date, total_cases, new_cases, total_deaths, population
From [Covid Deaths Project]..CovidDeaths

-- Total Cases Vs. Total Deaths in USA
-- Shows likelihood of dying if infected

Select Location, date, total_cases, total_deaths, round((total_deaths/total_cases) * 100,2) as Death_Percetage
From [Covid Deaths Project]..CovidDeaths
where Location like '%states%'
order by location, Death_Percetage

select  * from CovidDeaths
select  * from CovidVaccinations
-- Total Cases Vs. Polulation
-- Countriesm
select Location, max(total_cases) as total_cases , population , (Max(total_cases)/(population))*100 as Total_Infection_rate
from [Covid Deaths Project]..CovidDeaths
group by Location, population
Order by Total_Infection_rate


-- Countries with highest death count per population

select Location, max(cast(total_deaths as int)) Total_Deaths, population, round((max(cast(total_deaths as int))/population)*100,4) as DeathPercent
from [Covid Deaths Project]..CovidDeaths
where continent is not null
group by Location, population
order by DeathPercent Desc

-- Continents with highest death count per population

select Location, max(cast(total_deaths as int)) Total_Deaths, population, round((max(cast(total_deaths as int))/population)*100,4) as DeathPercent
from [Covid Deaths Project]..CovidDeaths
where continent is null
group by Location, population
order by DeathPercent Desc


-- GLOBAL NUMBERS

Select SUM(new_cases) as global_cases, SUM(cast(new_deaths as int)) as global_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From [Covid Deaths Project]..CovidDeaths
where continent is not null 
order by 1,2
	
-- Joining 2 tables
-- Total Vaccinated People

select CD.location,CD.population, max(CV.people_vaccinated) Total_Vaccinations, round((max(CV.people_vaccinated)/CD.population)* 100 ,2) Vaccination_Percentage from [Covid Deaths Project]..CovidDeaths as CD
join [Covid Deaths Project]..CovidVaccinations as CV
on CD.location = CV.location and
CD.date = CV.date
where CD.continent is not null
group by CD.location, CD.population
Order by Vaccination_Percentage desc

--- Rolling new Vaccinations 
--- Rolling calculations can be achieved by Datesinperiod() function of DAX

select CD.continent, CD.location, CD.date,CV.new_vaccinations, 
sum(cast(CV.new_vaccinations as int)) over (partition by CD.location order by CD.location, CD.date) as RoolingCountOfNewVaccinations
from [Covid Deaths Project]..CovidDeaths as CD
join [Covid Deaths Project]..CovidVaccinations as CV
	on CD.location = CV.location 
	and
	CD.date = CV.date
where CD.continent is not null


--- Using CTE calculating total percentage of new people vaccinated

with PopulationVSVacation (Continent, Location, Date, Population, RoolingCountOfNewVaccinations)
as (
	select CD.continent, CD.location, CD.date,CD.population, sum(cast(CV.new_vaccinations as int)) over (partition by CD.location order by CD.location, CD.date) as RoolingCountOfNewVaccinations
	from [Covid Deaths Project]..CovidDeaths as CD
	join [Covid Deaths Project]..CovidVaccinations as CV
		on CD.location = CV.location 
		and
		CD.date = CV.date
	where CD.continent is not null
)
select *, (RoolingCountOfNewVaccinations/Population)*100 as NewVaccinationPercent from PopulationVSVacation;


-- Temp Table for calculating total percentage of new people vaccinated

DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
Select CD.continent, CD.location, CD.date, CD.population, CD.new_vaccinations
, SUM(CONVERT(int,CV.new_vaccinations)) OVER (Partition by CD.Location Order by CD.location, CD.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From [Covid Deaths Project]..CovidDeaths as CD
Join [Covid Deaths Project]..CovidVaccinations as CV
	On CD.location = CV.location
	and CD.date = CV.date
--where dea.continent is not null 
--order by 2,3

Select *, (RollingPeopleVaccinated/Population)*100 as NewVaccinationPercent
From #PercentPopulationVaccinated


-- Creating View to store data for later visualizations


Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From [Covid Deaths Project]..CovidDeaths dea
Join [Covid Deaths Project]..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 

select * from PercentPopulationVaccinated
