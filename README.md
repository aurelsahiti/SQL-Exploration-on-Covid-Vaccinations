# SQL Project: Global COVID-19 Analysis  

**Prepared by Aurel Sahiti**  

---

## Project Overview
This project explores **global COVID-19 trends** using **SQL (Google BigQuery)**.  
It focuses on analyzing infection, mortality, and vaccination rates by country and continent to derive **data-driven insights about pandemic impact and vaccine distribution**.

---

## Objectives
- Explore how infection and death rates differ across countries and continents.  
- Quantify the **likelihood of dying** from COVID-19 (case fatality rate).  
- Measure the **percentage of population infected** over time.  
- Examine global **vaccination progress** and population coverage.  
- Build reusable SQL views and tables for downstream visualization (e.g., Tableau, Power BI).  

---

## Tools & Environment
- **Google BigQuery** – Cloud-based data warehouse  
- **SQL (Standard SQL Syntax)**  
- **Datasets:**  
  - `Covid.CovidDeaths`  
  - `Covid.CovidVaccinations`  

---

## Core SQL Concepts Demonstrated

### 1. Data Selection & Ordering
```sql
SELECT
  location, date, total_cases, new_cases, total_deaths, population
FROM
  `covid-project-5701.Covid.CovidDeaths`
ORDER BY 1,2;
```

Retrieves key metrics for trend visualization and national comparisons.

---

### 2. Total Cases vs. Total Deaths
```sql
SELECT
  location, date, total_cases, total_deaths,
  (total_deaths/total_cases)*100 AS death_percentage
FROM
  `covid-project-5701.Covid.CovidDeaths`
WHERE
  location = 'United States';
```

Calculates the fatality rate for each day in a selected country.

---

### 3. Total Cases vs. Population
```sql
SELECT
  location, date, total_cases, population,
  (total_cases/population)*100 AS contracted_percentage
FROM
  `covid-project-5701.Covid.CovidDeaths`
WHERE
  location = 'United States';
```

Shows the percent of population infected over time.

---

### 4. Infection Rate Ranking by Country
```sql
SELECT
  location, population,
  MAX(total_cases) AS highest_infection_count,
  MAX((total_cases/population))*100 AS percent_infected
FROM
  `covid-project-5701.Covid.CovidDeaths`
GROUP BY location, population
ORDER BY percent_infected DESC;
```

Identifies countries with highest infection rate relative to population.

---

### 5. Death Count per Population
```sql
SELECT
  location, MAX(total_deaths) AS total_death_count
FROM
  `covid-project-5701.Covid.CovidDeaths`
WHERE
  continent IS NOT NULL
GROUP BY location
ORDER BY total_death_count DESC;
```

Lists countries with the highest cumulative death toll.

---

### 6. Global Numbers
```sql
SELECT
  SUM(new_cases) AS total_cases,
  SUM(new_deaths) AS total_deaths,
  (SUM(new_deaths)/SUM(new_cases))*100 AS death_percentage
FROM
  `covid-project-5701.Covid.CovidDeaths`
WHERE
  continent IS NOT NULL;
```

Provides global COVID-19 metrics for dashboard summaries.

---

### 7. Population vs. Vaccination Progress (Join)
```sql
SELECT
  dea.continent, dea.location, dea.date, dea.population,
  vac.new_vaccinations,
  SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
    AS rolling_total_vaccinated
FROM
  `covid-project-5701.Covid.CovidDeaths` dea
JOIN
  `covid-project-5701.Covid.CovidVaccinations` vac
ON dea.location = vac.location AND dea.date = vac.date
WHERE
  dea.continent IS NOT NULL;
```

Combines death and vaccination data and calculates rolling vaccination totals.

---

### 8. Using a CTE for Cleaner Logic
```sql
WITH PopvsVac AS (
  SELECT
    dea.continent, dea.location, dea.date, dea.population,
    vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
      AS rolling_total_vaccinated
  FROM
    `covid-project-5701.Covid.CovidDeaths` dea
  JOIN
    `covid-project-5701.Covid.CovidVaccinations` vac
  ON dea.location = vac.location AND dea.date = vac.date
  WHERE dea.continent IS NOT NULL
)
SELECT
  *,
  (rolling_total_vaccinated/population)*100 AS population_vaccinated_percentage
FROM
  PopvsVac;
```

Calculates percent of each country’s population vaccinated over time.

---

### 9. Creating a Temporary Table
```sql
CREATE TEMP TABLE PercentPopulationVaccinated (
  continent STRING,
  location STRING,
  date DATETIME,
  population INT64,
  new_vaccinations INT64,
  rolling_total_vaccinated INT64
);
```

Demonstrates temporary table creation for intermediate analysis.

---

### 10. Creating a View for Visualization
```sql
CREATE VIEW Covid.PercentPopulationVaccinated AS
SELECT
  dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
  SUM(vac.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
    AS rolling_total_vaccinated
FROM
  `covid-project-5701.Covid.CovidDeaths` dea
JOIN
  `covid-project-5701.Covid.CovidVaccinations` vac
ON dea.location = vac.location AND dea.date = vac.date
WHERE
  dea.continent IS NOT NULL;
```

Creates a persistent SQL view for BI tools (Power BI, Tableau, Looker Studio).

---

## Key Insights

- European and North American countries exhibit the highest infection rates per capita.

- Case fatality rates declined steadily as vaccination coverage increased.

- Rolling vaccination totals allow comparison of vaccine rollout speeds across countries.

- SQL CTEs and window functions simplify large-scale population-based analytics.

---

## Next Steps

- Integrate output into Power BI or Tableau dashboards for visualization.

- Automate daily query execution with BigQuery scheduled queries.

- Expand with stored procedures or Cloud Functions for real-time analysis.

---

## Author
**Aurel Sahiti**  
Data Science Graduate Student | Exploratory Analytics & Visualization  
📊 [LinkedIn](https://linkedin.com/in/aurelsahiti) | 🌐 [GitHub](https://github.com/aurelsahiti)
