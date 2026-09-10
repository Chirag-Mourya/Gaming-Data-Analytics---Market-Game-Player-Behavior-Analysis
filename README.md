_**Gaming Analytics - Market & Player Behavior Analysis**_


**Project Overview**

This project is an end-to-end Gaming Analytics and Business
Intelligence project built to analyze a large gaming dataset and
uncover meaningful patterns across the gaming ecosystem.

The analysis focuses on:

Game release trends over time

Genre representation

Platform distribution

Publisher presence

Game popularity

User ratings

Metacritic scores

Player engagement

Average playtime

Game completion behavior

Multiplayer characteristics

ESRB rating distribution

The project combines Python for data exploration and preparation,
PostgreSQL for structured SQL analysis, and Power BI for interactive
business intelligence and visualization.

The final outcome is a one-page interactive Power BI dashboard designed
to provide an executive-level overview of the gaming market while
allowing users to explore the data through filters.


**Project Objectives**

Understand the structure and quality of the gaming dataset.

Identify patterns in game releases across different years.

Analyze genre and platform representation.

Examine game popularity, ratings, engagement, and playtime.

Understand publisher and developer distribution.

Analyze player-oriented metrics such as completion and engagement.

Build a reliable analytical data model.

Develop business-oriented SQL analysis.

Create an interactive Power BI dashboard that communicates key
findings clearly.


**Tools & Technologies**

Tool              Purpose

Python        Data exploration, cleaning and preparation
Pandas        Data manipulation and analysis
NumPy         Numerical operations
Matplotlib    Exploratory visualization
PostgreSQL    Relational data storage and SQL analysis
SQL           Querying, aggregation and validation
Power BI      Interactive dashboard and visualization
DAX           Measures and analytical calculations
Power Query   Data transformation and loading
Git/GitHub    Version control and project presentation


**Repository Structure**

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


**Project Workflow**

Raw Gaming Dataset
        ↓
Data Exploration & Understanding
        ↓
Data Cleaning & Preparation
        ↓
Feature Engineering
        ↓
Python / Pandas Analysis
        ↓
PostgreSQL Data Loading
        ↓
SQL Data Validation & Analysis
        ↓
Dimension Tables
        ↓
Power BI Data Model
        ↓
DAX Measures
        ↓
Interactive Dashboard
        ↓
Business Insights


_1. Data Exploration & Preparation_

Python and Pandas were used during the initial stage to understand the
dataset before moving into SQL and Power BI.

The exploration included:

Checking dataset dimensions

Inspecting column names and data types

Identifying missing values

Checking duplicate records

Understanding categorical fields

Examining numerical distributions

Investigating relationships between gaming metrics

Identifying unusual or incomplete records

Preparing fields for downstream analysis

Important fields included:

game_id

title

release_date

release_year

publishers

developers

user_rating

metacritic

popularity_score

engagement_score

avg_playtime_hours

status_owned

status_beaten

status_playing

status_dropped

status_toplay

status_yet


_2. Data Cleaning & Feature Engineering_

Several fields required preparation before they could be used
effectively for analysis.

Feature engineering included working with:

Release year and release date

Popularity metrics

Rating metrics

Playtime

Engagement

Ownership and completion status

Multiplayer indicators

Platform counts

Game mode information

Rating tiers

Additional analytical tables were created for:

genre_table

platform_table

store_table

tag_table

These tables helped separate multi-valued attributes from the main
game-level data and made category-based analysis easier.


_3. PostgreSQL & SQL Analysis_

The cleaned data was loaded into PostgreSQL for structured analysis.

SQL techniques used included:

SELECT

WHERE

GROUP BY

HAVING

ORDER BY

CASE

Aggregate functions

COUNT

COUNT(DISTINCT ...)

AVG

SUM

ROUND

NULLIF

JOIN

CTEs

Conditional aggregation

The analysis focused on realistic analytical questions rather than only
demonstrating SQL syntax.

Examples included:

Which games have the highest completion rates?

Which genres are most represented?

Which genres have unusually high popularity?

Which publishers have the largest game catalogs?

Which genres benefit most from multiplayer?

Which games combine high popularity with strong user ratings?

How has game release activity changed over time?

SQL was also used to validate results before using them in Power BI.


_4. Power BI Dashboard_

The final analysis was converted into a one-page interactive Power BI
dashboard.

KPI Cards

The dashboard highlights approximately:

Total Publishers: 6.28K

Total Games: 15K

Total Developers: 8.79K

Average Popularity: 24.93

Average Playtime: 3.72 hours

Average Metacritic: 71/100

Interactive Filters

The dashboard includes slicers for:

Date

Platform

Genre

Store

Game Mode

Visualizations

Games Released Over Time

Shows how the number of recorded games changes across release
years.

Most Represented Genre Categories

Shows which genre categories have the largest representation.

Platform Distribution

Shows the relative representation of major gaming platforms.

Top Publishers by Game Count

Highlights publishers with the largest recorded game catalogs.

ESRB Rating Distribution

Shows the distribution of games across ESRB categories.

Top Games by Popularity

Compares highly popular games using popularity scores and user
ratings.


**Key Findings**

1. Large and Fragmented Gaming Ecosystem

The dataset contains approximately:

15K games

6.28K publishers

8.79K developers

This indicates a highly fragmented ecosystem with many publishers and
developers contributing to the overall game catalog.


2. PC Has Strong Platform Representation

PC accounts for approximately 50.7% of the selected top-five
platform distribution.

Other major platforms shown include:

macOS --- ~17.6%

Linux --- ~12.1%

PlayStation 4 --- ~10.3%

Xbox One --- ~9.2%

This suggests that PC has a particularly strong presence within the
dataset.


3. Indie, Adventure and Action Are Highly Represented

The most represented genre categories include:

Indie

Adventure

Action

Casual

RPG

Simulation

Strategy

Indie has the largest recorded representation.

Because games can have multiple genre associations, these values should
be interpreted as genre-category representation rather than strictly
unique game counts.


4. Release Activity Increased Through the Mid-2010s

The release trend shows substantial growth from the 2000s into the
mid-2010s, with the highest recorded activity around 2016.

After that point, recorded releases decrease.

However, later years contain fewer and potentially incomplete records,
so this should not be interpreted as proof that the real-world gaming
industry declined after 2016.


5. Overall Critical Reception Is Moderately Positive

The dashboard reports an average Metacritic score of approximately
71/100, indicating moderately positive critical reception within the
available records.

6. A Small Group of Games Stands Out in Popularity

Highly popular games in the dataset include:

Grand Theft Auto V

The Witcher 3: Wild Hunt

Portal 2

Red Dead Redemption 2

Portal

The Elder Scrolls V: Skyrim

These titles combine strong popularity with relatively high user
ratings.


7. ESRB Data Has Significant Missing Coverage

Approximately 56.6% of the displayed ESRB distribution is classified
as Not Rated.

This is an important data-quality finding and means ESRB-based analysis
should be interpreted carefully.

**Data Quality & Limitations**

A major lesson from this project was that a technically correct query
does not automatically produce a real-world-valid conclusion.

The dataset contains several irregularities.

Missing Data

Some games have missing:

User ratings

Metacritic scores

Publishers

Developers

ESRB ratings

Platform information

Other metadata

These missing values can influence averages and distributions.

Unspecified Categories

Some records contain values such as:

Not Specified

Not Rated

These were handled carefully during analysis and visualization.

Multi-Valued Attributes

A game can be associated with multiple:

Genres

Platforms

Stores

Tags

For example:

Action
RPG

can both describe the same game.

This creates an important distinction between category associations
and unique game counts.

Incomplete Recent Years

Later release years contain fewer records.

Therefore, lower recorded game counts in recent years should not
automatically be interpreted as a decline in the real-world gaming
industry.


**Dataset vs Real-World Market**

This dashboard represents patterns found in the available dataset,
not official global gaming-market statistics.

Some values may therefore differ from external industry reports.


**Problems Faced & How They Were Solved**

Problem 1 - Understanding Multi-Valued Columns

Some games were associated with multiple genres, platforms, stores, or
tags. This caused duplicated game appearances during category-level
analysis.

Solution

Separate analytical tables were created for:

Genres

Platforms

Stores

Tags

This allowed category-level analysis while retaining game-level
information.


Problem 2 - Data Type Mismatch in SQL JOINs

One SQL challenge involved joining game_data.game_id with
genre_table.game_id when the fields had different data types.
PostgreSQL returned an error similar to:

operator does not exist: text = bigint

Solution

The join condition was adjusted using explicit type casting where
necessary.

This reinforced the importance of checking column data types before
joining tables.


Problem 3 - PostgreSQL ROUND() Type Error

PostgreSQL produced errors when ROUND() was used with incompatible
numeric types.

Solution

Values were explicitly cast to numeric before rounding:

ROUND(value::numeric, 2)


Problem 4 - Completion Rate Calculation

Completion rate required dividing games beaten by games owned. Direct
division could result in division-by-zero errors.

Solution

NULLIF() was used:

status_beaten / NULLIF(status_owned, 0)


Problem 5 - Misleading Growth Analysis

An initial comparison of recent release periods showed negative growth.

Further investigation showed that later years contained incomplete
records.

Solution

The trend was investigated instead of being blindly presented as a
real-world market decline. The data-quality limitation was documented.


Problem 6 - Implicit vs Explicit Measures

Power BI automatically created implicit measures such as
Count of game_id.

Solution

Explicit DAX measures were created, for example:

Total Games =
DISTINCTCOUNT(games_eda_ready[game_id])

This provided better control and clearer business definitions.


**What I Learned**

_Data Analysis_

Inspect data before analyzing it.

Understand what each field actually represents.

Identify missing and inconsistent data.

Investigate unexpected results.

Validate calculations instead of assuming they are correct.


_SQL_

Strengthened practical knowledge of:

Aggregations

Joins

CTEs

Conditional aggregation

CASE

HAVING

COUNT(DISTINCT)

Type casting

NULLIF

Business-oriented query design

The biggest SQL lesson was that a syntactically correct query is not
necessarily a meaningful analytical query.


_Power BI_

Learned how to:

Build interactive dashboards.

Create DAX measures.

Build calendar tables.

Use slicers.

Configure visual interactions.

Design KPI cards.

Select appropriate visualizations.

Apply consistent dashboard formatting.

Build a narrative around visualizations.

Analytical Thinking

The most important lesson was learning to question unexpected results.

**For example, a decline in recorded game releases after 2016 initially
looked like a market decline. Investigation revealed incomplete coverage
in later years.**


**Scope for Improvement**

1. Improve Data Completeness

Supplement the dataset with reliable sources for:

Recent game releases

Publisher information

ESRB ratings

Platform availability

Review data


2. Add Financial Metrics

If reliable financial data becomes available, future versions could
incorporate:

Revenue

Sales

Development cost

Marketing cost

Units sold

ROI

This would allow analysis of commercial success, not only popularity
and ratings.


3. Add Advanced Player Segmentation

Future analysis could segment games or player behavior using:

Playtime

Engagement

Completion

Rating behavior

Multiplayer preference


4. Add Statistical Analysis

Future versions could investigate:

Popularity vs ratings

Playtime vs engagement

Genre-level differences

Platform effects on popularity

Predictive modeling for popularity


5. Automate Data Refresh

A future implementation could connect Power BI to a regularly updated
source so that new games and recent records are incorporated
automatically.


**Final Takeaway**

This project demonstrates an end-to-end analytics workflow:

Explore
  ↓
Clean
  ↓
Transform
  ↓
Validate
  ↓
Analyze
  ↓
Model
  ↓
Visualize
  ↓
Interpret

The main outcome was not simply a Power BI dashboard. The project
emphasized data validation, analytical reasoning, SQL problem-solving,
data modeling, and business-focused visualization.

The final dashboard provides an interactive overview of the gaming
ecosystem and allows users to explore games by date, genre, platform,
store, and game mode.

👤 Author

Chirag Mourya

Data Analytics Project
Python • SQL • PostgreSQL • Power BI • DAX
