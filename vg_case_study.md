# 🎮 Video Game Sales Case Study

### **About this dataset**

This dataset provides detailed information on video games that have sold more than 100,000 copies worldwide, compiled from a scrape of VGChartz.com. Each entry includes the game’s name, platform of release, year of release, genre, and publisher, along with sales figures broken down by region. Sales are reported in millions of units for North America (NA_Sales), Europe (EU_Sales), Japan (JP_Sales), and other regions (Other_Sales), with a calculated global total (Global_Sales). The dataset also assigns an overall sales rank to each game, making it useful for analyzing industry trends, comparing regional preferences, and identifying the top-performing platforms, genres, and publishers over time.

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

1. Use **CAST(Year AS INT)** to convert the year into an integer for cleaner grouping.
2. Use **SUM(Global_Sales)** to calculate total global sales (in millions) for each year.
3. Multiply by **1,000,000** to convert sales from millions into full units.
4. Use **FORMAT(..., 'N0')** to add commas and remove decimals for readability.
5. Exclude rows with missing years using **WHERE Year IS NOT NULL**.
6. Apply **GROUP BY Year** so totals are calculated per year.
7. Use **ORDER BY SUM(Global_Sales) DESC** to sort years from highest to lowest sales.
   
**Answer**

2008 had the highest global video game sales, with about 43.23 billion units sold.

<img width="207" height="147" alt="Screenshot 2025-10-03 163023" src="https://github.com/user-attachments/assets/f944f336-74a0-4068-afc6-e0c41b0c886d" />
