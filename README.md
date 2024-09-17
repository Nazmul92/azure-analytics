
# Tokyo Olympics Data Analysis with Azure Data Factory, Data Lake, and Synapse Analytics


# Project Overview

This project focuses on conducting an end-to-end data analysis of the Tokyo Olympics dataset using Microsoft Azure services, including Azure Data Factory, Azure Data Lake, and Azure Synapse Analytics. The goal is to provide meaningful insights about athlete participation, gender representation, and medal performance across different countries and disciplines.

## Key Components:
- **Data Source**: CSV files for athletes, coaches, gender participation, medals, and teams.
- **Data Ingestion**: Azure Data Factory is used to ingest the data into Azure Data Lake.
- **Data Transformation**: Azure Synapse Analytics is used to process, transform, and analyze the data.
- **Visualization**: Each SQL query result is visualized using Synapse's built-in charting tools.

---

## Data Pipeline Design

The project uses the following data pipeline:

```plaintext
+-----------------------+      +------------------------+
|                       |      |                        |
|  Azure Data Factory    +----->|   Azure Data Lake      |
|                       |      |                        |
+-----------------------+      +------------------------+
                                      |
                                      v
+------------------------+      +------------------------+
|                        |      |                        |
|   Azure Synapse        |      |   SQL Queries &        |
|   Analytics            +----->|   Visualizations       |
|                        |      |                        |
+------------------------+      +------------------------+
```
## Data Flow:
- Ingestion: Data is ingested from the Tokyo Olympics CSV files into Azure Data Lake using Azure Data Factory.
- Transformation: The data is transformed and cleaned using Azure Synapse Analytics.
- Analysis: SQL queries are written and executed in Azure Synapse Analytics to perform detailed analysis on the data.
- Visualization: Each SQL query is visualized using the charting feature in Synapse Analytics, and the images are downloaded for documentation.

## Project Structure:
```plaintext
├── data
│   ├── athletes.csv
│   ├── coaches.csv
│   ├── entries_gender.csv
│   ├── medals.csv
│   └── teams.csv
├── sql
│   ├── athletes_country.sql
│   ├── gender_representation.sql
│   ├── medals_country.sql
│   └── team_participation.sql
├── images
│   ├── athletes_by_country.png
│   ├── gender_distribution.png
│   ├── medals_by_country.png
│   └── team_representation.png
└── README.md
```
## Data Analysis:
### Basic Table Information
#### Start by exploring the basic structure and data from each table.
```plaintext
SELECT TOP 10 *
FROM athletes;

SELECT TOP 10 *
FROM medals;
```
### Check for Null or Missing Value
#### Checking for null or missing values is crucial in identifying incomplete data.
```plaintext
SELECT COUNT(*) AS NullCount
FROM athletes
WHERE PersonName IS NULL OR Country IS NULL OR Discipline IS NULL;
```
## Athletes and Country Representation
### Athletes by Country
#### Query the number of athletes participating from each country.
```plaintext
SELECT COUNT(*) AS NullCount
FROM athletes
WHERE PersonName IS NULL OR Country IS NULL OR Discipline IS NULL;
```
### Athletes by Country
#### Query the number of athletes participating from each country.
```plaintext
SELECT Country, COUNT(*) AS AthleteCount
FROM athletes
GROUP BY Country
ORDER BY AthleteCount DESC;
```
### Top Disciplines by Athlete Participation
#### Find out which sports disciplines have the most athletes.
```plaintext
SELECT Discipline, COUNT(*) AS AthleteCount
FROM athletes
GROUP BY Discipline
ORDER BY AthleteCount DESC;
```
## Gender Representation Analysis
### Gender Breakdown by Discipline
#### Use the entries_gender table to analyze the gender distribution across different sports disciplines.
```plaintext
SELECT Discipline, Female, Male, Total, 
       (Female * 1.0 / Total) * 100 AS FemalePercentage,
       (Male * 1.0 / Total) * 100 AS MalePercentage
FROM entries_gender
ORDER BY Total DESC;
```
### Sports with the Highest Female Participation
#### Identify sports where female athletes dominate in terms of participation.
```plaintext
SELECT Discipline, Female, Total, 
       (Female * 1.0 / Total) * 100 AS FemalePercentage
FROM entries_gender
WHERE Female > Male
ORDER BY FemalePercentage DESC;
```
## Medal Performance by Country
### Total Medals by Country
#### Get a breakdown of medals won by each country.
```plaintext
SELECT TeamCountry, Gold, Silver, Bronze, Total
FROM medals
ORDER BY Total DESC;
```
### Top 10 Countries by Gold Medals
#### Focus on the countries with the highest number of gold medals.
```plaintext
SELECT TOP 10 TeamCountry, Gold
FROM medals
ORDER BY Gold DESC;
```
### Medal Contribution by Discipline
#### Analyze which disciplines contribute the most to the overall medal tally.
```plaintext
SELECT Discipline, SUM(m.Gold) AS Gold, SUM(m.Silver) AS Silver, SUM(m.Bronze) AS Bronze, SUM(m.Total) AS TotalMedals
FROM teams t
JOIN medals m ON t.Country = m.TeamCountry
GROUP BY Discipline
ORDER BY TotalMedals DESC;
```
## Team Participation Analysis
### Number of Teams per Country
#### Get the number of teams from each country participating in the Olympics.
```plaintext
SELECT Country, COUNT(DISTINCT TeamName) AS TeamCount
FROM teams
GROUP BY Country
ORDER BY TeamCount DESC;
```
### Top Disciplines by Team Representation
#### Find out which disciplines have the most teams representing different countries.
```plaintext
SELECT Discipline, COUNT(DISTINCT TeamName) AS TeamCount
FROM teams
GROUP BY Discipline
ORDER BY TeamCount DESC;
```
## Correlation Analysis Between Athlete Participation and Medals
### Correlation Between Number of Athletes and Total Medals
#### Analyze whether the number of athletes a country sends correlates with its medal count.
```plaintext
SELECT a.Country, COUNT(a.PersonName) AS AthleteCount, m.Total AS TotalMedals
FROM athletes a
JOIN medals m ON a.Country = m.TeamCountry
GROUP BY a.Country, m.Total
ORDER BY TotalMedals DESC;
```
## Summary of Findings
#### Based on the analysis above, you can summarize insights such as:
- Country Performance: Which countries performed best in terms of medals, and what disciplines contributed the most?
- Gender Representation: What is the gender breakdown across different sports? Are some sports more male- or female-dominated?
- Team and Athlete Participation: How many athletes and teams represented each country, and did more athletes result in more medals?
## Key Takeaways:
- Medal Dominance: Countries like the United States, China, and Japan dominate the medal tally.
- Athlete Representation: Countries with larger athlete contingents often perform better.
- Gender Balance: Certain sports like artistic swimming and rhythmic gymnastics have higher female representation, while others like boxing are male-dominated.
