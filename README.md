# Gaming Analytics Dashboard

An end-to-end data analytics project exploring **15K+ games** to understand gaming market trends, genres, platforms, publishers, popularity, ratings, playtime, and player engagement.

The project combines **Python, PostgreSQL, SQL, Power BI, and DAX** to transform raw gaming data into an interactive one-page dashboard.

---

## Project Overview

The goal of this project was to move through a realistic analytics workflow:

**Raw Data → EDA & Cleaning → Data Modeling → SQL Analysis → DAX → Power BI Dashboard → Insights**

The analysis focuses on questions such as:

- How has game release activity changed over time?
- Which genres are most represented?
- Which platforms dominate the dataset?
- Which publishers have the largest game catalogs?
- Which games have the highest popularity?
- How do popularity and user ratings compare?
- What does the ESRB distribution look like?
- How do engagement and playtime vary across games and genres?

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| **Python / Pandas** | Data exploration, cleaning and preparation |
| **PostgreSQL** | Data storage and SQL analysis |
| **SQL** | Aggregation, joins, filtering and business questions |
| **Power BI** | Dashboard and interactive visualization |
| **DAX** | KPI and analytical measures |
| **Power Query** | Data transformation |
| **GitHub** | Project documentation and version control |

---

## Project Workflow

```text
Gaming Dataset
      ↓
Exploratory Data Analysis
      ↓
Data Cleaning & Preparation
      ↓
Feature Engineering
      ↓
PostgreSQL
      ↓
SQL Analysis & Validation
      ↓
Dimension Tables
      ↓
Power BI Data Model
      ↓
DAX Measures
      ↓
Interactive Dashboard
```

---

## Power BI Dashboard

The dashboard provides a one-page overview of the gaming ecosystem.

### KPIs
- Total Games
- Total Publishers
- Total Developers
- Average Popularity
- Average Playtime
- Average Metacritic Score

### Interactive Filters
- Date
- Platform
- Genre
- Store
- Game Mode

### Main Visuals
- Games Released Over Time
- Top Genre Categories
- Platform Distribution
- Top Publishers by Game Count
- ESRB Rating Distribution
- Top Games by Popularity

---

## Key Findings

### Large & Fragmented Market
The dataset contains approximately **15K games, 6.28K publishers, and 8.79K developers**, indicating a highly fragmented gaming ecosystem.

### PC Dominance
PC represents approximately **50.7%** of the selected top-five platform distribution, making it the most represented platform in the dataset.

### Genre Representation
**Indie, Adventure, Action, Casual, and RPG** are among the most represented genre categories.

### Release Trend
Recorded game releases increased substantially through the 2000s and peaked around **2016**. Later years show fewer records, but this should not automatically be interpreted as a real-world decline.

### Game Popularity
Games such as **Grand Theft Auto V, The Witcher 3, Portal 2, and Red Dead Redemption 2** stand out among the highest-popularity titles.

### Critical Reception
The overall average Metacritic score is approximately **71/100**, suggesting moderately positive critical reception within the available records.

### ESRB Coverage
A large share of records is classified as **Not Rated**, highlighting a significant metadata gap in the dataset.

---

## Challenges & Solutions

### 1. Multi-valued attributes
Games can have multiple genres, platforms, stores, and tags, which can create duplicate-looking records during analysis.

**Solution:** Created separate `genre_table`, `platform_table`, `store_table`, and `tag_table` tables for category-level analysis.

### 2. SQL data-type mismatch
Joining `game_id` columns with different data types produced PostgreSQL errors such as:

```text
operator does not exist: text = bigint
```

**Solution:** Used appropriate type casting and ensured compatible join keys.

### 3. PostgreSQL rounding errors
`ROUND()` produced errors when incompatible numeric types were used.

**Solution:**

```sql
ROUND(value::numeric, 2)
```

### 4. Division by zero
Completion-rate calculations could fail when the denominator was zero.

**Solution:**

```sql
NULLIF(status_owned, 0)
```

### 5. Unexpected release trends
The apparent decline in later release years initially looked like a market decline.

**Solution:** Investigated the underlying data and identified incomplete recent-year coverage before drawing conclusions.

### 6. Power BI implicit measures
Power BI automatically created measures such as `Count of game_id`.

**Solution:** Created explicit DAX measures such as:

```DAX
Total Games =
DISTINCTCOUNT(games_eda_ready[game_id])
```

---

## Data Quality & Limitations

This dataset contains several irregularities, including:

- Missing user ratings and Metacritic scores
- Missing or unspecified publishers and other metadata
- `Not Rated` ESRB records
- Multi-valued genres, platforms, stores, and tags
- Incomplete records for some later release years

Therefore, the dashboard should be interpreted as an analysis of **patterns within the available dataset**, not as an exact representation of the global gaming industry.

In particular, the lower number of games recorded in recent years should **not** be treated as definitive evidence of declining industry output.

---

## What I Learned

This project strengthened my practical understanding of:

- Data cleaning and exploratory analysis
- SQL joins and aggregations
- CTEs and conditional logic
- PostgreSQL data types and error handling
- Data modeling
- DAX measures
- Power BI dashboard design
- Interactive filtering
- Data validation and interpretation

The biggest lesson was that **getting a technically correct result is not enough**. Unexpected results need to be investigated, validated, and interpreted in the context of data quality and collection methods.

---

## Future Improvements

Possible improvements include:

- Add more complete and regularly updated gaming data
- Build a more robust star-schema data model
- Add revenue, sales, and commercial-performance metrics
- Add player segmentation
- Analyze relationships between popularity, ratings, engagement, and playtime
- Add predictive modeling for game popularity
- Automate data refreshes

---

## Repository Structure

```text
Gaming-Analytics/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── python/
│   └── gaming_eda.ipynb
│
├── sql/
│   ├── schema.sql
│   └── analysis.sql
│
├── powerbi/
│   └── gaming_analytics_dashboard.pbix
│
├── images/
│   └── dashboard_preview.png
│
└── README.md
```

---

## Author

**Chirag Mourya**
**Data Analytics | Python | SQL | PostgreSQL | Power BI | DAX**
