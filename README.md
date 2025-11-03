# Global Renewable Energy & Carbon Emissions SQL Database

![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)

A comprehensive SQL Server database for analyzing global renewable energy capacity, carbon emissions, and energy investments. Supports UN SDG 7 (Affordable Clean Energy) & SDG 13 (Climate Action).

**This repository is constantly being updated and added to by the community. Pull requests are welcome. Enjoy!**

---

## 📊 Quick Stats
- **7 Global Regions** | **15 Countries** | **23 Major Energy Projects**
- **Time Period:** 2020-2023
- **100+ Records** across renewable capacity, emissions, consumption, and investments

---

## Table of Contents
1. [Database Setup](#1-database-setup)
2. [Basic Data Retrieval Queries](#2-basic-data-retrieval-queries)
3. [Filtering & Searching Queries](#3-filtering--searching-queries)
4. [Aggregation & Analysis Queries](#4-aggregation--analysis-queries)
5. [Join Queries for Multi-Table Analysis](#5-join-queries-for-multi-table-analysis)
6. [Advanced Analytical Queries](#6-advanced-analytical-queries)
7. [Data Modification Queries](#7-data-modification-queries)
8. [View Creation Queries](#8-view-creation-queries)
9. [Performance Optimization](#9-performance-optimization)
10. [Use Cases & Examples](#10-use-cases--examples)

---

## 1. Database Setup

### Prerequisites
- Microsoft SQL Server 2016+
- SQL Server Management Studio (SSMS) or Azure Data Studio

### Installation
```sql
-- Clone and execute the database_setup.sql file
-- All tables will be created with sample data included
```

### Database Schema
```
regions (7 records)
    ├── countries (15 records)
         ├── renewable_capacity (20 records)
         ├── energy_consumption (20 records)
         ├── carbon_emissions (20 records)
         ├── energy_investments (20 records)
         └── energy_projects (23 records)
```

### Quick Verification
```sql
-- Check all tables exist
SELECT TABLE_NAME 
FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_TYPE = 'BASE TABLE';

-- Verify record counts
SELECT 
    'regions' AS table_name, COUNT(*) AS records FROM regions
UNION ALL SELECT 'countries', COUNT(*) FROM countries
UNION ALL SELECT 'renewable_capacity', COUNT(*) FROM renewable_capacity
UNION ALL SELECT 'energy_consumption', COUNT(*) FROM energy_consumption
UNION ALL SELECT 'carbon_emissions', COUNT(*) FROM carbon_emissions
UNION ALL SELECT 'energy_investments', COUNT(*) FROM energy_investments
UNION ALL SELECT 'energy_projects', COUNT(*) FROM energy_projects;
```

---

## 2. Basic Data Retrieval Queries

### SELECT: Retrieve all records from a table
```sql
-- Get all countries
SELECT * FROM countries;

-- Get all regions
SELECT * FROM regions;

-- Get all energy projects
SELECT * FROM energy_projects;
```

### SELECT specific columns
```sql
-- Get country names and codes only
SELECT country_name, country_code FROM countries;

-- Get project names and capacities
SELECT project_name, capacity_mw, project_type FROM energy_projects;
```

### DISTINCT: Get unique values
```sql
-- Get all project types
SELECT DISTINCT project_type FROM energy_projects;

-- Get all project statuses
SELECT DISTINCT status FROM energy_projects;

-- Get all years in dataset
SELECT DISTINCT year FROM renewable_capacity ORDER BY year;
```

### ORDER BY: Sort results
```sql
-- Countries by population (descending)
SELECT country_name, population_millions 
FROM countries 
ORDER BY population_millions DESC;

-- Projects by capacity (ascending)
SELECT project_name, capacity_mw 
FROM energy_projects 
ORDER BY capacity_mw ASC;

-- Multi-column sorting
SELECT country_name, gdp_per_capita_usd, population_millions
FROM countries
ORDER BY gdp_per_capita_usd DESC, population_millions DESC;
```

### TOP: Limit results
```sql
-- Top 5 most populous countries
SELECT TOP 5 country_name, population_millions 
FROM countries 
ORDER BY population_millions DESC;

-- Top 10 largest energy projects
SELECT TOP 10 project_name, capacity_mw, project_type
FROM energy_projects
ORDER BY capacity_mw DESC;

-- Top 3 regions by GDP
SELECT TOP 3 region_name, gdp_usd_billions
FROM regions
ORDER BY gdp_usd_billions DESC;
```

---

## 3. Filtering & Searching Queries

### WHERE: Filter records with conditions
```sql
-- Countries with high energy poverty
SELECT country_name, energy_poverty_rate 
FROM countries 
WHERE energy_poverty_rate > 10;

-- Operational solar projects
SELECT project_name, capacity_mw 
FROM energy_projects 
WHERE project_type = 'Solar' AND status = 'Operational';

-- Countries in Asia with population over 100 million
SELECT c.country_name, c.population_millions, r.region_name
FROM countries c
JOIN regions r ON c.region_id = r.region_id
WHERE r.region_name LIKE '%Asia%' AND c.population_millions > 100;
```

### AND, OR, NOT operators
```sql
-- Large projects (>1000 MW) that are Solar or Wind
SELECT project_name, project_type, capacity_mw
FROM energy_projects
WHERE capacity_mw > 1000 AND (project_type = 'Solar' OR project_type = 'Wind');

-- Countries NOT in Europe or North America
SELECT c.country_name, r.region_name
FROM countries c
JOIN regions r ON c.region_id = r.region_id
WHERE NOT (r.region_name LIKE '%Europe%' OR r.region_name LIKE '%North America%');
```

### BETWEEN: Range filtering
```sql
-- Projects completed between 2015 and 2020
SELECT project_name, completion_date, status
FROM energy_projects
WHERE completion_date BETWEEN '2015-01-01' AND '2020-12-31';

-- Countries with medium population (50-150 million)
SELECT country_name, population_millions
FROM countries
WHERE population_millions BETWEEN 50 AND 150;

-- Emissions in a specific range
SELECT country_id, year, total_co2_mt
FROM carbon_emissions
WHERE total_co2_mt BETWEEN 400 AND 700
ORDER BY total_co2_mt;
```

### IN: Multiple value matching
```sql
-- Specific countries
SELECT country_name, country_code 
FROM countries 
WHERE country_code IN ('CHN', 'USA', 'IND', 'DEU', 'BRA');

-- Specific project types
SELECT project_name, project_type, capacity_mw
FROM energy_projects
WHERE project_type IN ('Solar', 'Wind', 'Hybrid');

-- Specific years
SELECT country_id, year, total_renewable_capacity_mw
FROM renewable_capacity
WHERE year IN (2020, 2023);
```

### LIKE: Pattern matching
```sql
-- Countries starting with 'U'
SELECT country_name FROM countries WHERE country_name LIKE 'U%';

-- Projects with 'Solar' in the name
SELECT project_name, project_type 
FROM energy_projects 
WHERE project_name LIKE '%Solar%';

-- Regions containing 'Asia'
SELECT region_name FROM regions WHERE region_name LIKE '%Asia%';

-- Projects ending with 'Farm' or 'Park'
SELECT project_name FROM energy_projects 
WHERE project_name LIKE '%Farm' OR project_name LIKE '%Park';
```

### NULL: Check for missing values
```sql
-- Countries without energy poverty data
SELECT country_name, energy_poverty_rate 
FROM countries 
WHERE energy_poverty_rate IS NULL;

-- Projects without completion dates
SELECT project_name, status 
FROM energy_projects 
WHERE completion_date IS NULL;
```

---

## 4. Aggregation & Analysis Queries

### COUNT: Count records
```sql
-- Total number of projects
SELECT COUNT(*) AS total_projects FROM energy_projects;

-- Count by project type
SELECT project_type, COUNT(*) AS project_count
FROM energy_projects
GROUP BY project_type
ORDER BY project_count DESC;

-- Count operational projects
SELECT COUNT(*) AS operational_projects 
FROM energy_projects 
WHERE status = 'Operational';
```

### SUM: Calculate totals
```sql
-- Total renewable capacity globally (2023)
SELECT SUM(total_renewable_capacity_mw) AS global_capacity_2023
FROM renewable_capacity
WHERE year = 2023;

-- Total investment in renewables by country (2023)
SELECT 
    c.country_name,
    SUM(ei.renewable_investment_usd_millions) AS total_investment
FROM energy_investments ei
JOIN countries c ON ei.country_id = c.country_id
WHERE ei.year = 2023
GROUP BY c.country_name
ORDER BY total_investment DESC;
```

### AVG: Calculate averages
```sql
-- Average CO2 per capita by region (2023)
SELECT 
    r.region_name,
    AVG(ce.per_capita_co2_tons) AS avg_co2_per_capita
FROM carbon_emissions ce
JOIN countries c ON ce.country_id = c.country_id
JOIN regions r ON c.region_id = r.region_id
WHERE ce.year = 2023
GROUP BY r.region_name
ORDER BY avg_co2_per_capita DESC;

-- Average renewable percentage by country
SELECT 
    c.country_name,
    AVG(ec.renewable_percentage) AS avg_renewable_pct
FROM energy_consumption ec
JOIN countries c ON ec.country_id = c.country_id
GROUP BY c.country_name
ORDER BY avg_renewable_pct DESC;
```

### MIN and MAX: Find extremes
```sql
-- Highest and lowest energy poverty rates
SELECT 
    MAX(energy_poverty_rate) AS highest_poverty,
    MIN(energy_poverty_rate) AS lowest_poverty
FROM countries
WHERE energy_poverty_rate > 0;

-- Largest and smallest energy projects
SELECT 
    MAX(capacity_mw) AS largest_project,
    MIN(capacity_mw) AS smallest_project
FROM energy_projects;
```

### GROUP BY: Group aggregated data
```sql
-- Total capacity by project type
SELECT 
    project_type,
    COUNT(*) AS project_count,
    SUM(capacity_mw) AS total_capacity,
    AVG(capacity_mw) AS avg_capacity
FROM energy_projects
GROUP BY project_type
ORDER BY total_capacity DESC;

-- Annual emissions by country
SELECT 
    c.country_name,
    ce.year,
    ce.total_co2_mt
FROM carbon_emissions ce
JOIN countries c ON ce.country_id = c.country_id
ORDER BY c.country_name, ce.year;
```

### HAVING: Filter grouped results
```sql
-- Countries with average renewable percentage above 40%
SELECT 
    c.country_name,
    AVG(ec.renewable_percentage) AS avg_renewable
FROM energy_consumption ec
JOIN countries c ON ec.country_id = c.country_id
GROUP BY c.country_name
HAVING AVG(ec.renewable_percentage) > 40
ORDER BY avg_renewable DESC;

-- Project types with total capacity over 10,000 MW
SELECT 
    project_type,
    SUM(capacity_mw) AS total_capacity
FROM energy_projects
GROUP BY project_type
HAVING SUM(capacity_mw) > 10000;
```

---

## 5. Join Queries for Multi-Table Analysis

### INNER JOIN: Matching records from both tables
```sql
-- Countries with their regions
SELECT 
    c.country_name,
    c.population_millions,
    r.region_name
FROM countries c
INNER JOIN regions r ON c.region_id = r.region_id;

-- Projects with country information
SELECT 
    ep.project_name,
    ep.project_type,
    ep.capacity_mw,
    c.country_name
FROM energy_projects ep
INNER JOIN countries c ON ep.country_id = c.country_id
WHERE ep.status = 'Operational';
```

### Multiple INNER JOINs: Three or more tables
```sql
-- Complete project overview with country and region
SELECT 
    ep.project_name,
    ep.project_type,
    ep.capacity_mw,
    ep.status,
    c.country_name,
    r.region_name
FROM energy_projects ep
INNER JOIN countries c ON ep.country_id = c.country_id
INNER JOIN regions r ON c.region_id = r.region_id
ORDER BY ep.capacity_mw DESC;

-- Renewable capacity with emissions data
SELECT 
    c.country_name,
    rc.year,
    rc.total_renewable_capacity_mw,
    ce.total_co2_mt,
    ec.renewable_percentage
FROM renewable_capacity rc
INNER JOIN countries c ON rc.country_id = c.country_id
INNER JOIN carbon_emissions ce ON rc.country_id = ce.country_id AND rc.year = ce.year
INNER JOIN energy_consumption ec ON rc.country_id = ec.country_id AND rc.year = ec.year
WHERE rc.year = 2023;
```

### LEFT JOIN: All records from left table
```sql
-- All countries with their projects (including countries without projects)
SELECT 
    c.country_name,
    COUNT(ep.project_id) AS project_count
FROM countries c
LEFT JOIN energy_projects ep ON c.country_id = ep.country_id
GROUP BY c.country_name
ORDER BY project_count DESC;
```

### Self JOIN: Join table to itself
```sql
-- Compare countries in the same region
SELECT 
    c1.country_name AS country1,
    c2.country_name AS country2,
    c1.gdp_per_capita_usd AS gdp1,
    c2.gdp_per_capita_usd AS gdp2
FROM countries c1
INNER JOIN countries c2 ON c1.region_id = c2.region_id
WHERE c1.country_id < c2.country_id
ORDER BY c1.region_id;
```

---

## 6. Advanced Analytical Queries

### Year-over-Year Growth Analysis
```sql
-- Renewable capacity growth by country
WITH capacity_growth AS (
    SELECT 
        country_id,
        year,
        total_renewable_capacity_mw,
        LAG(total_renewable_capacity_mw) OVER (PARTITION BY country_id ORDER BY year) AS prev_year_capacity
    FROM renewable_capacity
)
SELECT 
    c.country_name,
    cg.year,
    cg.total_renewable_capacity_mw,
    cg.prev_year_capacity,
    ROUND(((cg.total_renewable_capacity_mw - cg.prev_year_capacity) / cg.prev_year_capacity * 100), 2) AS growth_percentage
FROM capacity_growth cg
JOIN countries c ON cg.country_id = c.country_id
WHERE cg.prev_year_capacity IS NOT NULL
ORDER BY c.country_name, cg.year;
```

### Ranking Analysis
```sql
-- Rank countries by renewable capacity (2023)
SELECT 
    c.country_name,
    rc.total_renewable_capacity_mw,
    RANK() OVER (ORDER BY rc.total_renewable_capacity_mw DESC) AS capacity_rank
FROM renewable_capacity rc
JOIN countries c ON rc.country_id = c.country_id
WHERE rc.year = 2023;

-- Top 3 projects by CO2 reduction in each country
SELECT * FROM (
    SELECT 
        c.country_name,
        ep.project_name,
        ep.co2_reduction_mt_per_year,
        ROW_NUMBER() OVER (PARTITION BY c.country_name ORDER BY ep.co2_reduction_mt_per_year DESC) AS rank
    FROM energy_projects ep
    JOIN countries c ON ep.country_id = c.country_id
    WHERE ep.status = 'Operational'
) ranked
WHERE rank <= 3;
```

### Common Table Expressions (CTE)
```sql
-- Calculate investment efficiency
WITH investment_summary AS (
    SELECT 
        country_id,
        year,
        renewable_investment_usd_millions,
        fossil_fuel_investment_usd_millions,
        (renewable_investment_usd_millions + fossil_fuel_investment_usd_millions) AS total_investment
    FROM energy_investments
),
capacity_summary AS (
    SELECT 
        country_id,
        year,
        total_renewable_capacity_mw
    FROM renewable_capacity
)
SELECT 
    c.country_name,
    i.year,
    i.renewable_investment_usd_millions,
    cs.total_renewable_capacity_mw,
    ROUND(i.renewable_investment_usd_millions / cs.total_renewable_capacity_mw, 2) AS investment_per_mw
FROM investment_summary i
JOIN capacity_summary cs ON i.country_id = cs.country_id AND i.year = cs.year
JOIN countries c ON i.country_id = c.country_id
WHERE i.year = 2023
ORDER BY investment_per_mw;
```

### UNION: Combine results from multiple queries
```sql
-- All Solar and Wind projects combined
SELECT project_name, project_type, capacity_mw, 'Solar' AS energy_category
FROM energy_projects 
WHERE project_type = 'Solar'
UNION
SELECT project_name, project_type, capacity_mw, 'Wind' AS energy_category
FROM energy_projects 
WHERE project_type = 'Wind'
ORDER BY capacity_mw DESC;
```

### Subqueries
```sql
-- Countries with above-average renewable capacity (2023)
SELECT 
    c.country_name,
    rc.total_renewable_capacity_mw
FROM renewable_capacity rc
JOIN countries c ON rc.country_id = c.country_id
WHERE rc.year = 2023 
    AND rc.total_renewable_capacity_mw > (
        SELECT AVG(total_renewable_capacity_mw) 
        FROM renewable_capacity 
        WHERE year = 2023
    )
ORDER BY rc.total_renewable_capacity_mw DESC;

-- Projects in countries with high energy poverty
SELECT 
    ep.project_name,
    c.country_name,
    c.energy_poverty_rate
FROM energy_projects ep
JOIN countries c ON ep.country_id = c.country_id
WHERE c.country_id IN (
    SELECT country_id 
    FROM countries 
    WHERE energy_poverty_rate > 10
);
```

### CASE: Conditional logic
```sql
-- Categorize countries by renewable adoption
SELECT 
    c.country_name,
    ec.renewable_percentage,
    CASE 
        WHEN ec.renewable_percentage >= 50 THEN 'High Renewable'
        WHEN ec.renewable_percentage >= 30 THEN 'Medium Renewable'
        WHEN ec.renewable_percentage >= 15 THEN 'Low Renewable'
        ELSE 'Very Low Renewable'
    END AS renewable_category
FROM energy_consumption ec
JOIN countries c ON ec.country_id = c.country_id
WHERE ec.year = 2023
ORDER BY ec.renewable_percentage DESC;

-- Classify projects by size
SELECT 
    project_name,
    capacity_mw,
    CASE 
        WHEN capacity_mw >= 5000 THEN 'Mega Project'
        WHEN capacity_mw >= 1000 THEN 'Large Project'
        WHEN capacity_mw >= 100 THEN 'Medium Project'
        ELSE 'Small Project'
    END AS project_size
FROM energy_projects
WHERE status = 'Operational'
ORDER BY capacity_mw DESC;
```

---

## 7. Data Modification Queries

### INSERT: Add new records
```sql
-- Add a new country
INSERT INTO countries (country_id, country_name, country_code, region_id, population_millions, gdp_per_capita_usd, land_area_km2, energy_poverty_rate)
VALUES (16, 'France', 'FRA', 2, 67.4, 43518.00, 643801.00, 0.10);

-- Add a new energy project
INSERT INTO energy_projects (country_id, project_name, project_type, capacity_mw, investment_usd_millions, start_date, status, co2_reduction_mt_per_year, jobs_created)
VALUES (4, 'Baltic Sea Wind Farm', 'Wind', 1200.00, 3500.00, '2023-01-15', 'Under Construction', 2100.00, 1500);

-- Insert multiple records
INSERT INTO renewable_capacity (country_id, year, solar_capacity_mw, wind_capacity_mw, hydro_capacity_mw, total_renewable_capacity_mw)
VALUES 
(16, 2023, 15800.00, 21400.00, 25600.00, 62800.00),
(16, 2022, 14200.00, 19800.00, 25500.00, 59500.00);
```

### UPDATE: Modify existing records
```sql
-- Update project status
UPDATE energy_projects 
SET status = 'Operational', completion_date = '2024-03-15'
WHERE project_name = 'Xinjiang Solar Phase 4';

-- Update country data
UPDATE countries 
SET population_millions = 1428.50, gdp_per_capita_usd = 13200.00
WHERE country_code = 'CHN';

-- Increase investment values by 10%
UPDATE energy_investments
SET renewable_investment_usd_millions = renewable_investment_usd_millions * 1.10
WHERE year = 2023;
```

### DELETE: Remove records
```sql
-- Delete a specific project
DELETE FROM energy_projects 
WHERE project_name = 'Test Project';

-- Delete records based on condition
DELETE FROM renewable_capacity 
WHERE year < 2020;

-- Delete all records from a table (use with caution!)
DELETE FROM energy_projects WHERE status = 'Cancelled';
```

---

## 8. View Creation Queries

### CREATE VIEW: Create reusable queries
```sql
-- View: Latest country statistics (2023)
CREATE VIEW vw_country_stats_2023 AS
SELECT 
    c.country_name,
    c.population_millions,
    c.gdp_per_capita_usd,
    rc.total_renewable_capacity_mw,
    ec.renewable_percentage,
    ce.per_capita_co2_tons
FROM countries c
LEFT JOIN renewable_capacity rc ON c.country_id = rc.country_id AND rc.year = 2023
LEFT JOIN energy_consumption ec ON c.country_id = ec.country_id AND ec.year = 2023
LEFT JOIN carbon_emissions ce ON c.country_id = ce.country_id AND ce.year = 2023;

-- View: Operational projects summary
CREATE VIEW vw_operational_projects AS
SELECT 
    ep.project_name,
    ep.project_type,
    ep.capacity_mw,
    ep.co2_reduction_mt_per_year,
    ep.jobs_created,
    c.country_name,
    r.region_name
FROM energy_projects ep
JOIN countries c ON ep.country_id = c.country_id
JOIN regions r ON c.region_id = r.region_id
WHERE ep.status = 'Operational';

-- View: Investment trends
CREATE VIEW vw_investment_trends AS
SELECT 
    c.country_name,
    ei.year,
    ei.renewable_investment_usd_millions,
    ei.fossil_fuel_investment_usd_millions,
    ROUND((ei.renewable_investment_usd_millions / 
          (ei.renewable_investment_usd_millions + ei.fossil_fuel_investment_usd_millions) * 100), 2) 
          AS renewable_share_pct
FROM energy_investments ei
JOIN countries c ON ei.country_id = c.country_id;
```

### SELECT from VIEW
```sql
-- Query the view like a regular table
SELECT * FROM vw_country_stats_2023
ORDER BY renewable_percentage DESC;

SELECT country_name, renewable_share_pct 
FROM vw_investment_trends
WHERE year = 2023
ORDER BY renewable_share_pct DESC;
```

### DROP VIEW: Remove a view
```sql
DROP VIEW vw_country_stats_2023;
DROP VIEW vw_operational_projects;
DROP VIEW vw_investment_trends;
```

---

## 9. Performance Optimization

### CREATE INDEX: Improve query performance
```sql
-- Create index on frequently queried columns
CREATE INDEX idx_country_code ON countries(country_code);
CREATE INDEX idx_project_type ON energy_projects(project_type);
CREATE INDEX idx_year ON renewable_capacity(year);
CREATE INDEX idx_status ON energy_projects(status);

-- Composite index for multi-column queries
CREATE INDEX idx_country_year ON renewable_capacity(country_id, year);
CREATE INDEX idx_project_country_status ON energy_projects(country_id, status);
```

### Query Execution Plan
```sql
-- View execution plan (before running actual query)
SET SHOWPLAN_TEXT ON;
GO

SELECT c.country_name, SUM(ep.capacity_mw) AS total_capacity
FROM energy_projects ep
JOIN countries c ON ep.country_id = c.country_id
GROUP BY c.country_name;

SET SHOWPLAN_TEXT OFF;
GO
```

---

## 10. Use Cases & Examples

### Use Case 1: Renewable Energy Transition Dashboard
```sql
-- Complete country energy profile
SELECT 
    c.country_name,
    c.population_millions,
    rc.total_renewable_capacity_mw,
    ec.renewable_percentage,
    ec.per_capita_kwh,
    ce.per_capita_co2_tons,
    ei.renewable_investment_usd_millions,
    COUNT(ep.project_id) AS active_projects
FROM countries c
LEFT JOIN renewable_capacity rc ON c.country_id = rc.country_id AND rc.year = 2023
LEFT JOIN energy_consumption ec ON c.country_id = ec.country_id AND ec.year = 2023
LEFT JOIN carbon_emissions ce ON c.country_id = ce.country_id AND ce.year = 2023
LEFT JOIN energy_investments ei ON c.country_id = ei.country_id AND ei.year = 2023
LEFT JOIN energy_projects ep ON c.country_id = ep.country_id AND ep.status = 'Operational'
GROUP BY c.country_name, c.population_millions, rc.total_renewable_capacity_mw, 
         ec.renewable_percentage, ec.per_capita_kwh, ce.per_capita_co2_tons, 
         ei.renewable_investment_usd_millions;
```

### Use Case 2: Investment ROI Analysis
```sql
-- Calculate investment efficiency and CO2 impact
SELECT 
    c.country_name,
    SUM(ep.investment_usd_millions) AS total_project_investment,
    SUM(ep.capacity_mw) AS total_capacity,
    SUM(ep.co2_reduction_mt_per_year) AS total_co2_reduction,
    ROUND(SUM(ep.investment_usd_millions) / SUM(ep.capacity_mw), 2) AS cost_per_mw,
    ROUND(SUM(ep.co2_reduction_mt_per_year) / SUM(ep.investment_usd_millions), 4) AS co2_reduction_per_million_usd
FROM energy_projects ep
JOIN countries c ON ep.country_id = c.country_id
WHERE ep.status = 'Operational'
GROUP BY c.country_name
ORDER BY co2_reduction_per_million_usd DESC;
```

### Use Case 3: Regional Comparison
```sql
-- Regional renewable energy performance
SELECT 
    r.region_name,
    COUNT(DISTINCT c.country_id) AS countries,
    SUM(c.population_millions) AS total_population,
    AVG(ec.renewable_percentage) AS avg_renewable_pct,
    SUM(rc.total_renewable_capacity_mw) AS total_capacity,
    AVG(ce.per_capita_co2_tons) AS avg_co2_per_capita
FROM regions r
JOIN countries c ON r.region_id = c.region_id
LEFT JOIN renewable_capacity rc ON c.country_id = rc.country_id AND rc.year = 2023
LEFT JOIN energy_consumption ec ON c.country_id = ec.country_id AND ec.year = 2023
LEFT JOIN carbon_emissions ce ON c.country_id = ce.country_id AND ce.year = 2023
GROUP BY r.region_name
ORDER BY avg_renewable_pct DESC;
```

### Use Case 4: Energy Poverty Targeting
```sql
-- Identify high-impact countries for renewable investment
SELECT 
    c.country_name,
    c.energy_poverty_rate,
    c.population_millions,
    ROUND(c.population_millions * c.energy_poverty_rate / 100, 2) AS affected_population_millions,
    rc.total_renewable_capacity_mw,
    COUNT(ep.project_id) AS existing_projects
FROM countries c
LEFT JOIN renewable_capacity rc ON c.country_id = rc.country_id AND rc.year = 2023
LEFT JOIN energy_projects ep ON c.country_id = ep.country_id
WHERE c.energy_poverty_rate > 5
GROUP BY c.country_name, c.energy_poverty_rate, c.population_millions, rc.total_renewable_capacity_mw
ORDER BY affected_population_millions DESC;
```

### Use Case 5: Technology Mix Analysis
```sql
-- Analyze renewable technology distribution by country
SELECT 
    c.country_name,
    rc.year,
    ROUND((rc.solar_capacity_mw / rc.total_renewable_capacity_mw * 100), 2) AS solar_pct,
    ROUND((rc.wind_capacity_mw / rc.total_renewable_capacity_mw * 100), 2) AS wind_pct,
    ROUND((rc.hydro_capacity_mw / rc.total_renewable_capacity_mw * 100), 2) AS hydro_pct,
    rc.total_renewable_capacity_mw
FROM renewable_capacity rc
JOIN countries c ON rc.country_id = c.country_id
WHERE rc.year = 2023
ORDER BY rc.total_renewable_capacity_mw DESC;
```

---

## 📚 Additional Resources

### Sample Reports
- [Country Rankings Report](queries/reports/country_rankings.sql)
- [Emission Trends Analysis](queries/reports/emission_analysis.sql)
- [Investment Flow Report](queries/reports/investment_patterns.sql)

### Data Dictionary
- [Complete Field Definitions](documentation/data_dictionary.md)
- [Calculation Methodologies](documentation/calculations.md)

### Visual Resources
- [Database ER Diagram](documentation/erd_diagram.png)
- [Sample Dashboard Screenshots](visualizations/dashboards/)

---

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Add New Queries**: Share useful SQL patterns
2. **Improve Documentation**: Clarify examples or add explanations
3. **Report Issues**: Found a bug? Let us know
4. **Suggest Features**: Ideas for new tables or data points

### How to Contribute
```bash
# Fork the repository
# Create your feature branch
git checkout -b feature/AmazingQuery

# Commit your changes
git commit -m 'Add amazing analysis query'

# Push to the branch
git push origin feature/AmazingQuery

# Open a Pull Request
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---
