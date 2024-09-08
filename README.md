COVID-19 Dashboard
=========================

[![Dashboard Snapshot](images/Covid Dash.png)](https://public.tableau.com/shared/48FJ5RZB7?:display_count=n&:origin=viz_share_link)

### Dashboard Link:

[View Dashboard](https://public.tableau.com/shared/48FJ5RZB7?:display_count=n&:origin=viz_share_link)

### SQL Link:

[View SQL Script](https://github.com/AlexanderHolland/Portfolio-Projects/blob/main/Covid%20Portfolio.sql)

Our project focuses on analyzing COVID-19 data to understand the impact and trends globally and by continent. The following steps outline how we derived key insights from the data using SQL.

Objective
---------

To analyze COVID-19 data by exploring infection rates, death counts, vaccination progress, and creating visualizations to support data-driven decision-making.

Problem Statement
-----------------

The analysis aims to provide insights into infection rates, death counts, and vaccination coverage across different regions. The goal is to identify trends, high-impact areas, and vaccination progress globally and by continent.

### Key Metrics:

*   **Total Cases:** Total number of COVID-19 cases reported.
*   **Total Deaths:** Total number of deaths due to COVID-19.
*   **Death Percentage:** Percentage of deaths among total cases.
*   **Contraction Percentage:** Percentage of the population that contracted COVID-19.
*   **Rolling People Vaccinated:** Cumulative count of people vaccinated over time.
*   **Percentage People Vaccinated:** Percentage of the population that has received at least one vaccine dose.

SQL Queries and Steps Followed:
-------------------------------

### Initial Data Selection:

    SELECT *
    FROM COVID_DEATHS
    -- Loading and previewing the COVID-19 deaths data.

### Data Selection and Conversion:

    SELECT LOCATION, TO_DATE(DATE_, 'DD/MM/YYYY') AS ORDER_DATE, NEW_CASES, TOTAL_DEATHS, POPULATION
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    ORDER BY LOCATION, ORDER_DATE
    -- Selecting the relevant columns and converting the date format.

### Cases vs Deaths:

    SELECT LOCATION, TO_DATE(DATE_, 'DD/MM/YYYY') AS DATE, TOTAL_CASES, TOTAL_DEATHS, 
           (TOTAL_DEATHS / TOTAL_CASES) * 100 AS DEATH_PERCENTAGE
    FROM COVID_DEATHS
    WHERE LOCATION LIKE '%Kingdom%'
      AND CONTINENT IS NOT NULL
    ORDER BY LOCATION, DATE
    -- Calculating the death percentage in the United Kingdom.

### Cases vs Population:

    SELECT LOCATION, TO_DATE(DATE_, 'DD/MM/YYYY') AS DATE, TOTAL_CASES, POPULATION, 
           (TOTAL_CASES / POPULATION) * 100 AS CONTRACTION_PERCENTAGE
    FROM COVID_DEATHS
    WHERE LOCATION LIKE '%Kingdom%'
      AND CONTINENT IS NOT NULL
    ORDER BY LOCATION, DATE
    -- Calculating the contraction percentage for the United Kingdom.

### Global Contraction Percentage:

    SELECT LOCATION, TO_DATE(DATE_, 'DD/MM/YYYY') AS DATE, TOTAL_CASES, POPULATION, 
           (TOTAL_CASES / POPULATION) * 100 AS CONTRACTION_PERCENTAGE
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    ORDER BY LOCATION, DATE
    -- Calculating the contraction percentage globally.

### Countries with Highest Infection Rate:

    SELECT LOCATION, POPULATION, MAX(TOTAL_CASES) AS "HIGHEST INFECTION COUNT", 
           NVL(MAX((TOTAL_CASES / POPULATION) * 100), 0) AS "HIGHEST CONTRACTION PERCENTAGE"
    FROM COVID_DEATHS
    GROUP BY LOCATION, POPULATION
    ORDER BY "HIGHEST CONTRACTION PERCENTAGE" DESC
    -- Identify countries with the highest infection rates per population.

### Countries with Highest Death Count:

    SELECT LOCATION, NVL(MAX(TOTAL_DEATHS), 0) AS "TOTAL DEATH COUNT"
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    GROUP BY LOCATION
    ORDER BY "TOTAL DEATH COUNT" DESC
    -- Identify countries with the highest death counts.

### Continents with Highest Death Count:

    SELECT CONTINENT, NVL(MAX(TOTAL_DEATHS), 0) AS "TOTAL DEATH COUNT"
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    GROUP BY CONTINENT
    ORDER BY "TOTAL DEATH COUNT" DESC
    -- Analyze death counts by continent.

### Continents with Highest Infection Rate:

    SELECT CONTINENT, SUM(TOTAL_CASES) AS TOTAL_CASES, SUM(POPULATION) AS POPULATION, 
           (SUM(TOTAL_CASES) / SUM(POPULATION)) * 100 AS "INFECTION_RATE_PERCENTAGE"
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    GROUP BY CONTINENT
    ORDER BY "INFECTION_RATE_PERCENTAGE" DESC
    -- Analyze infection rates by continent.

### Global Deaths and Death Percentage:

    SELECT SUM(NEW_CASES) AS TOTAL_CASES, SUM(NEW_DEATHS) AS TOTAL_DEATHS, 
           (SUM(NEW_DEATHS) / SUM(NEW_CASES)) * 100 AS DEATH_PERCENTAGE
    FROM COVID_DEATHS
    WHERE CONTINENT IS NOT NULL
    -- Calculate global deaths and death percentage.

### Vaccination Data:

    SELECT DATE, SUM(TOTAL_VACCINATIONS) AS ROLLING_PEOPLE_VACCINATED, 
           (SUM(TOTAL_VACCINATIONS) / SUM(POPULATION)) * 100 AS PERCENTAGE_PEOPLE_VACCINATED
    FROM COVID_VACCINATIONS
    GROUP BY DATE
    ORDER BY DATE
    -- Analyze vaccination progress over time.

Snapshot of Dashboard (Tableau):
--------------------------------

[![Dashboard Snapshot](images/Covid Dash.png)](https://public.tableau.com/shared/48FJ5RZB7?:display_count=n&:origin=viz_share_link)

### Insights:

*   **Busiest Day:** Friday had the highest number of COVID-19 cases and deaths.
*   **Busiest Month:** July saw the most cases and deaths, reflecting a significant impact.
*   **Continent Insights:** Continent-specific data shows varying infection and death rates, highlighting regions with higher impacts.
*   **Vaccination Progress:** Data shows the percentage of vaccinated individuals over time.

### Summary:

This project provides an in-depth analysis of COVID-19 data, offering valuable insights into infection rates, death counts, and vaccination progress globally and by continent. The data-driven approach supports effective decision-making and public health strategies.
