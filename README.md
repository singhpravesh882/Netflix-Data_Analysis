# **Netflix Data Analysis using SQL & Python**

## **1. Introduction**
**Project Title:** Netflix Data Cleaning and Analysis using SQL in SSMS  
**Objective:** Perform data cleaning and analysis on the Netflix dataset using SQL in SSMS and visualize insights with Python.  
**Dataset:** [Netflix Titles Dataset from Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)  

---

## **2. Project Workflow**
1. **Downloaded dataset from Kaggle**
2. **Loaded data into SQL Server using SSMS**
3. **Performed data cleaning using SQL**
4. **Analyzed data using SQL queries**
5. **Visualized insights using Python (pandas, matplotlib, seaborn)**

---

## **3. Data Preprocessing & Setup**

### **Step 1: Loading Data into SQL Server**
- Imported `netflix_titles.csv` using Pandas in Jupyter Notebook.
- Verified schema and column names.
- Loaded data into SQL Server using **SQL Server Import Wizard**.

**Python Code:**
```python
import pandas as pd
df = pd.read_csv('netflix_titles.csv')
df.head()
```

**SQL Code:**
```sql
SELECT * FROM netflix_data;
```

### **Step 2: Data Cleaning in SQL**
- Removed duplicates.
- Handled foreign characters by updating datatype of `title` to `nvarchar`.
- Standardized date formats and missing values.
- CREATED NEW TABLE FOR LISTED_IN, DIRECTOR, COUNTRY, CAST (Why?, Because they have multiple values in it which becomes difficult when doing the analysis)

**Example Cleaning Queries:**
```sql
-- Updating title column to handle foreign characters
ALTER TABLE netflix_data ALTER COLUMN title NVARCHAR(255);

--  Populate missing values in Country (Basically this means that we need to replace null values by country by mapping the country table)
INSERT INTO netflix_country -- Once the below is completed, all the show_id that had null value country will be added with country name after mapping
SELECT show_id, m.country --here m.country will represent the values of country from the inner join that is used for mapping 
FROM netflix_data nd
INNER JOIN(
SELECT director, country
FROM netflix_country nc
INNER JOIN netflix_directors AS nd ON nc.show_id=nd.show_id
GROUP BY director, country)m on nd.director=m.director -- nd.director is the Null values which will be matched with m.director and replace the NULL values with country name in inner join
WHERE nd.country is NULL --so we have 831 rows where country is NULL 
					  --(So basically, we need to populate all show_id whose country is NULL in netflix_country and should not be NULL)
```

---

## **4. Data Analysis in SQL**


### **Key SQL Queries Used for Analysis**
#### 1. FOR EACH DIRECTOR, COUNT THE NUMBER OF MOVIES AND SHOWS CREATED BY THEM IN SEPARATE COLUMNS:
```sql
SELECT COUNT(distinct n.type) AS distinct_type, nd.director
, COUNT(distinct case when n.type = 'Movie' then n.show_id end) as no_of_movies
, COUNT(distinct case when n.type = 'TV Show' then n.show_id end) as no_of_shows
FROM netflix n
INNER JOIN netflix_directors nd ON n.show_id=nd.show_id
GROUP BY nd.director
HAVING COUNT(distinct n.type)>1
ORDER BY distinct_type DESC
```

#### 2️⃣ WHICH COUNTRY HAS HIGHEST NUMBER OF COMEDY MOVIES:
```sql
SELECT count (distinct ng.show_id) as no_of_movies, nc.country, n.type, ng.genre
FROM netflix_genre ng
INNER JOIN netflix_country nc ON nc.show_id=ng.show_id --this will join the country and genre tables to find out countries with highest comedies
inner join netflix n ON n.show_id = ng.show_id --this will join netflix and genre table to filter type only to movies and not comedy shows
WHERE ng.genre = 'Comedies' AND n.type='Movie'
GROUP BY nc.country, n.type, ng.genre
ORDER BY no_of_movies DESC --just to confirm the type and genre, I have added the columns for them too
```
---

## **5. Data Visualization using Python**
After querying data in SQL, visualizations were created using Python:`

---

## **6. Key Insights**
- **Netflix has more movies than TV shows.**
- **The most common genres are Drama, International Movies, and Comedies.**
- **Content production peaked in the last decade.**

---

## **7. Challenges & Learnings**
- Encountered foreign character issues, resolved using `nvarchar`.
- Optimized SQL queries with indexing for better performance.

---

## **8. Conclusion & Next Steps**
- SQL was effective in cleaning and analyzing Netflix data.
- Future improvements: Implementing a **dashboard using Power BI/Tableau** for real-time insights.

---


