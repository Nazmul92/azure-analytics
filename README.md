
# Tokyo Olympics Data Analysis with Azure Data Factory, Data Lake, and Synapse Analytics

## 1. Project Overview
This project focuses on conducting an end-to-end data analysis of the Tokyo Olympics dataset using Microsoft Azure services, including Azure Data Factory, Azure Data Lake, and Azure Synapse Analytics. The goal is to provide meaningful insights about athlete participation, gender representation, and medal performance across different countries and disciplines.
## 2. Tools & Technologies Used:
- Azure Data Factory: For data ingestion and transformation.
- Azure Data Lake: For storing raw and transformed data.
- Azure Synapse Analytics: For SQL-based data analysis and visualization.
- Power BI: For optional additional visualizations and reporting.
## 3. Key Components:
- **Data Source**: CSV files for athletes, coaches, gender participation, medals, and teams.
- **Data Ingestion**: Azure Data Factory is used to ingest the data into Azure Data Lake.
- **Data Transformation**: Azure Synapse Analytics is used to process, transform, and analyze the data.
- **Visualization**: Each SQL query result is visualized using Synapse's built-in charting tools.

---

## 4. Data Pipeline Design

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
## 5. Data Flow:
- Ingestion: Data is ingested from the Tokyo Olympics CSV files into Azure Data Lake using Azure Data Factory.
- Transformation: The data is transformed and cleaned using Azure Synapse Analytics.
- Analysis: SQL queries are written and executed in Azure Synapse Analytics to perform detailed analysis on the data.
- Visualization: Each SQL query is visualized using the charting feature in Synapse Analytics, and the images are downloaded for documentation.

## 6. Project Structure:
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
## 7. Data Analysis:
### 7.1 Basic Table Information
#### Start by exploring the basic structure and data from each table.
```plaintext
SELECT TOP 10 *
FROM athletes;

SELECT TOP 10 *
FROM medals;
```
### 7.2 Check for Null or Missing Value
#### Checking for null or missing values is crucial in identifying incomplete data.
```plaintext
SELECT COUNT(*) AS NullCount
FROM athletes
WHERE PersonName IS NULL OR Country IS NULL OR Discipline IS NULL;
```
## 8. Athletes and Country Representation
### 8.1 Athletes by Country
#### Query the number of athletes participating from each country.
```plaintext
SELECT COUNT(*) AS NullCount
FROM athletes
WHERE PersonName IS NULL OR Country IS NULL OR Discipline IS NULL;
```
### 8.2 Athletes by Country
#### Query the number of athletes participating from each country.
```plaintext
SELECT TOP 10 Country, COUNT(*) AS AthleteCount
FROM athletes
GROUP BY Country
ORDER BY AthleteCount DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Top_10_country_by_athletes_count.png)
### 8.3 Top Disciplines by Athlete Participation
#### Find out which sports disciplines have the most athletes.
```plaintext
SELECT TOP 10 Discipline, COUNT(*) AS AthleteCount
FROM athletes
GROUP BY Discipline
ORDER BY AthleteCount DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Top_10_discipline_by_athletesCount.png)

## 9. Gender Representation Analysis
### 9.1 Gender Breakdown by Discipline
#### Use the entries_gender table to analyze the gender distribution across different sports disciplines.
```plaintext
SELECT Discipline, Female, Male, Total, 
       (Female * 1.0 / Total) * 100 AS FemalePercentage,
       (Male * 1.0 / Total) * 100 AS MalePercentage
FROM entries_gender
ORDER BY Total DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Male_female_percentage_by_discipline.png)

### 9.2 Sports with the Highest Female Participation
#### Identify sports where female athletes dominate in terms of participation.
```plaintext
SELECT Discipline, Female, Total, 
       (Female * 1.0 / Total) * 100 AS FemalePercentage
FROM entries_gender
WHERE Female > Male
ORDER BY FemalePercentage DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Highest_female_participation_by_discipline.png)
## 10. Medal Performance by Country
### 10.1 Total Medals by Country
#### Get a breakdown of medals won by each country.
```plaintext
SELECT TeamCountry, Gold, Silver, Bronze, Total
FROM medals
ORDER BY Total DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Total_medals_by_country.png)
### 10.2 Top 10 Countries by Gold Medals
#### Focus on the countries with the highest number of gold medals.
```plaintext
SELECT TOP 10 TeamCountry, Gold
FROM medals
ORDER BY Gold DESC;
```
### 10.3 Medal Contribution by Discipline
#### Analyze which disciplines contribute the most to the overall medal tally.
```plaintext
SELECT Discipline, SUM(m.Gold) AS Gold, SUM(m.Silver) AS Silver, SUM(m.Bronze) AS Bronze, SUM(m.Total) AS TotalMedals
FROM teams t
JOIN medals m ON t.Country = m.TeamCountry
GROUP BY Discipline
ORDER BY TotalMedals DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Total_medals_by_discipline.png)
## 11. Team Participation Analysis
### 11.1 Number of Teams per Country
#### Get the number of teams from each country participating in the Olympics.
```plaintext
SELECT Country, COUNT(DISTINCT TeamName) AS TeamCount
FROM teams
GROUP BY Country
ORDER BY TeamCount DESC;
```
### 11.2 Top Disciplines by Team Representation
#### Find out which disciplines have the most teams representing different countries.
```plaintext
SELECT Discipline, COUNT(DISTINCT TeamName) AS TeamCount
FROM teams
GROUP BY Discipline
ORDER BY TeamCount DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Top_discipline_by_teams.png)
## 12. Correlation Analysis Between Athlete Participation and Medals
### Correlation Between Number of Athletes and Total Medals
#### Analyze whether the number of athletes a country sends correlates with its medal count.
```plaintext
SELECT a.Country, COUNT(a.PersonName) AS AthleteCount, m.Total AS TotalMedals
FROM athletes a
JOIN medals m ON a.Country = m.TeamCountry
GROUP BY a.Country, m.Total
ORDER BY TotalMedals DESC;
```
![Top 10 Disciplines by Athletes](https://github.com/Nazmul92/azure-analytics/raw/main/visualization-graphs/Correlation_athletes_total_madels.png)
## Analysis Insights
### Athletes and Country Representation:
- Top Countries by Athlete Count: The top countries sending athletes to the Tokyo Olympics are dominated by countries like the USA, China, and Japan.
### Gender Representation:
- Gender Distribution: Some sports, such as Artistic Swimming, have a higher participation of female athletes, while sports like Wrestling show a higher male participation.
### Medal Performance:
- Top Medal-Winning Countries: The United States, China, and Japan are among the top medal-winning countries, with the USA leading in gold medals.
### Team Participation:
- Team Representation: Countries like the United States and Russia have the highest number of teams competing across various disciplines.

## Conclusion
This project demonstrates how to efficiently use Azure Data Factory, Data Lake, and Synapse Analytics to perform a detailed analysis of the Tokyo Olympics dataset. The analysis covers athlete participation, gender representation, and medal performance by country and discipline, providing actionable insights into the event.
