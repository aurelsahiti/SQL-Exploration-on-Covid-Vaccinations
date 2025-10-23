# SQL Project: Global COVID-19 Analysis  

**Prepared by Aurel Sahiti**  

---

## 🧭 Project Overview
This project explores **global COVID-19 trends** using **SQL (Google BigQuery)**.  
It focuses on analyzing infection, mortality, and vaccination rates by country and continent to derive **data-driven insights about pandemic impact and vaccine distribution**.

---

## 🎯 Objectives
- Explore how infection and death rates differ across countries and continents.  
- Quantify the **likelihood of dying** from COVID-19 (case fatality rate).  
- Measure the **percentage of population infected** over time.  
- Examine global **vaccination progress** and population coverage.  
- Build reusable SQL views and tables for downstream visualization (e.g., Tableau, Power BI).  

---

## ⚙️ Tools & Environment
- **Google BigQuery** – Cloud-based data warehouse  
- **SQL (Standard SQL Syntax)**  
- **Datasets:**  
  - `Covid.CovidDeaths`  
  - `Covid.CovidVaccinations`  

---

## 🧩 Core SQL Concepts Demonstrated

### 1️⃣ Data Selection & Ordering
```sql
SELECT
  location, date, total_cases, new_cases, total_deaths, population
FROM
  `covid-project-5701.Covid.CovidDeaths`
ORDER BY 1,2;
```

Retrieves key metrics for trend visualization and national comparisons.

---

