# 🎮 Video Game Sales Case Study

### **About this dataset**

This dataset, sourced from VGChartz.com, contains video games that sold more than 100,000 copies worldwide between 1980 and 2017. Each record includes the game’s title, platform, release year, genre, publisher, and sales figures by region (North America, Europe, Japan, and Others), along with total global sales (in millions). The data is useful for analyzing trends across time, comparing regional preferences, and identifying the top-performing platforms, genres, and publishers in the video game industry.

## Business Question

What factors drove global video game sales between 1980 and 2017, and how did platforms, publishers, genres, and regions influence success in the gaming industry?

## Research Question 1  
**Which year had the highest global video game sales?**

**SQL Query**
```sql
SELECT CAST(Year AS INT) AS Year,
       SUM(Global_Sales) AS Total_Global_Sales
FROM vgsales
WHERE Year IS NOT NULL
GROUP BY CAST(Year AS INT)
ORDER BY Total_Global_Sales DESC;
```


**Steps:**

- Convert Year into an integer for cleaner grouping.

- Use SUM(Global_Sales) to calculate total sales for each year.

- Exclude rows with missing years.

- Group by year and order results by total sales (highest first).
   
**Answer:**

2008 had the highest global video game sales, with about 43.23 billion units sold.

<img width="207" height="147" alt="Screenshot 2025-10-03 163023" src="https://github.com/user-attachments/assets/f944f336-74a0-4068-afc6-e0c41b0c886d" />

## Research Question 2

**Which year had the highest global video game sales?**

**SQL Query**
```sql
WITH platform_totals AS (
    SELECT Platform, SUM(Global_Sales) AS total_sales
    FROM vgsales
    GROUP BY Platform
),
top_platform AS (
    SELECT TOP 1 Platform
    FROM platform_totals
    ORDER BY total_sales DESC
)
SELECT TOP 10
    Name,
    Platform,
    Genre,
    Publisher,
    FORMAT(SUM(Global_Sales) * 1000000, 'N0') AS [Game Sales (Units)]
FROM vgsales
WHERE Platform = (SELECT Platform FROM top_platform)
GROUP BY Name, Platform, Genre, Publisher
ORDER BY SUM(Global_Sales) DESC;
```

**Steps:**

- Use a CTE to sum total global sales by platform.

- Select the top platform with the highest sales.

- Filter results to only include that platform.

- Group by game and sum total sales per game.

- Order by total sales and return the top 10 games.

**Answer:**

The analysis shows that the PlayStation 2 (PS2) was the top-selling platform overall, with global sales far exceeding other consoles. The top contributors to its success were blockbuster titles such as Grand Theft Auto: San Andreas, Gran Turismo 3, Gran Turismo 4, and Grand Theft Auto: Vice City, each selling hundreds of millions of units. These games played a major role in making the PS2 the best-selling platform of its generation.

<img width="585" height="206" alt="image" src="https://github.com/user-attachments/assets/6f9c5cc8-4a88-4b25-9a02-c6ef9ab54194" />

## Research Question 3

**How do regional sales (North America, Europe, Japan) differ, and which genres are most popular in each region?**

**SQL Query**
```sql
SELECT 
    Genre,
    FORMAT(SUM(NA_Sales) * 1000000, 'N0') AS [NA Sales (Units)],
    FORMAT(SUM(EU_Sales) * 1000000, 'N0') AS [EU Sales (Units)],
    FORMAT(SUM(JP_Sales) * 1000000, 'N0') AS [JP Sales (Units)]
FROM vgsales
GROUP BY Genre
ORDER BY SUM(NA_Sales) DESC;
```

**Steps:**
- Sum up NA_Sales, EU_Sales, and JP_Sales by Genre.

- Multiply each by 1,000,000 to convert from millions into units.

- Use ROUND(..., 2) to clean up long decimals.

- Group by Genre so totals are calculated per genre for each region.

- Order by North America sales first (you could also run it separately for EU and JP).

- The results show clear differences in regional preferences:

**Answer:**
The analysis shows clear differences in regional gaming preferences. In North America, Sports and Action titles dominate sales, reflecting the popularity of fast-paced and competitive games. In Europe, Action and Racing games are particularly successful, suggesting a strong market for immersive and dynamic experiences. Meanwhile, Japan shows a very different pattern, with Role-Playing Games (RPGs) far ahead of other genres, highlighting the region’s unique cultural preference for story-driven and character-focused titles. These differences emphasize the importance of region-specific publishing strategies in the gaming industry.

<img width="385" height="246" alt="image" src="https://github.com/user-attachments/assets/5bec7a00-2eee-402e-b3cc-eb39b7f6afab" />
