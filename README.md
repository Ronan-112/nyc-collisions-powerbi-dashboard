# NYC Motor Vehicle Collisions: End-to-End Analytics Dashboard

An end-to-end analytics project on **2.27 million police-reported NYC crash records**. Raw CSV to cleaned data, MySQL, and an interactive 3-page Power BI dashboard.

**Tools:** Python (Pandas, NumPy), MySQL, Power BI (DAX), Jupyter Notebook, Git/GitHub

---

## Dashboard preview

| Home | Business Analysis | Report |
|---|---|---|
| ![Home](images/home.png) | ![Business Analysis](images/business_analysis.png) | ![Report](images/report.png) |

---

## Business problem

Traffic safety teams need to know **when, where and why** crashes happen so they can reduce injuries and deaths. The raw data is a very large flat file with missing and inconsistent values, so it is hard to read or filter. This project turns it into a trusted, filterable dashboard.

## Key numbers (verified at every stage)

| Measure | Value |
|---|---|
| Total crashes | 2,269,187 |
| Persons injured | 756,353 |
| Persons killed | 3,617 |
| Fatal crashes | 3,469 |

These four numbers match across the raw file, Python, MySQL and Power BI.

## Pipeline

```
NYC Open Data (CSV)
      |
Python: profile, clean, standardize     -> collisions_clean.csv
      |
MySQL: table, indexes, reporting view   -> vw_crashes
      |
Power BI: data model, DAX, dashboard    -> 3 pages
```

### 1. Explore raw data (`notebooks/01_explore_raw_data.ipynb`)
- Loaded all columns as text to see the data exactly as stored.
- Checked missing values, date and time formats, coordinates, duplicates and spelling problems.

### 2. Clean data (`notebooks/02_clean.ipynb`)
- Renamed columns to snake_case, trimmed spaces, dropped the redundant `location` column.
- Converted date and time, created `year`, `month`, `month_name`, `day_of_week`, `hour`.
- Set invalid or out-of-NYC coordinates to NULL (kept the crash row).
- Filled missing borough and zip code with `UNKNOWN` instead of deleting rows.
- Converted injury and death counts to integers, created `total_casualties` and `severity` (Fatal, Injury, Property damage only).
- Standardized contributing factors and grouped **1,328 vehicle type labels into 10 groups** with keyword rules.
- Ran quality checks (unique IDs, valid hours, allowed severity values) and reconciled row counts.

### 3. Load into MySQL (`notebooks/03_load_mysql.ipynb`, `sql/`)
- Created a typed table with a primary key on `collision_id`.
- Loaded 2.27 million rows in chunks and reconciled the totals.
- Created 5 indexes and a reporting view `vw_crashes`.
- Wrote KPI queries (yearly trend, borough ranking, top causes, peak hours).

### 4. Power BI (`powerbi/`)
- Connected to the MySQL view, built a date table and a one-to-many relationship.
- Created DAX measures: Total Crashes, Total Injured, Total Killed, Fatal Crashes, Injured per Crash, Fatal per 1000 Crashes, Crashes LY, YoY Change %.
- Built dynamic insight measures that rewrite themselves for the selected year, borough or severity.
- Custom theme (`traffic_theme.json`), synced slicers, page navigation and bookmarks.

## Dashboard pages

| Page | What it shows |
|---|---|
| **Home** | KPI cards, yearly trend, crashes by borough, crash location map |
| **Business Analysis** | Top crash causes, weekday and hour heatmap, vehicle groups, severity split, injuries by road user |
| **Report** | Crash-level detail table and dynamic insight cards (top cause, busiest day and hour, worst street, change vs last year) |

## Key findings

1. Yearly crashes peaked in 2018 (231,564) and fell to 85,546 in 2025, with a sharp drop in 2020 (112,918).
2. Injuries per crash roughly doubled between 2019 (0.29) and 2025 (0.58), so fewer crashes does not mean each crash is less serious. A possible reason is that minor crashes are reported less often; this is a hypothesis, not a proven cause.
3. Driver inattention or distraction is the top cause (462,105 crashes, about 20% of all crashes). About 34% of crashes have an unspecified cause.
4. The busiest time is weekday afternoons, peaking on Friday between 4 and 5 PM. These are crash counts, not rates adjusted for traffic volume.
5. About 30.5% of crashes have no borough recorded, and they account for roughly 40% of deaths (1,441). The dashboard shows this as `UNKNOWN` instead of hiding it.
6. Pedestrians and cyclists are injured less often than motorists, but their share of deaths per injury is higher.

## Data notes and limitations

- **Source:** [NYC Open Data, Motor Vehicle Collisions - Crashes](https://data.cityofnewyork.us/) (police form MV-104AN).
- The source dataset was **last updated on July 31, 2026**, so 2012 and 2026 are partial years. Year-over-year numbers for these years should not be read as real declines.
- The data is preliminary and can change when forms are amended. For official fatality statistics, use the NYPD Motor Vehicle Collisions page or Vision Zero View.
- About 11% of crashes have no coordinates, so the map shows only crashes with a recorded location.
- The map visual shows a sample for large data, so it shows hotspots, not exact counts.
- Injury totals by pedestrian, cyclist and motorist do not add up exactly to total persons injured in the source data. Headline KPIs use the total persons columns.

## Repository structure

```
nyc-collisions-powerbi-dashboard/
  notebooks/    explore, clean and load notebooks
  sql/          table, index, view and KPI query scripts
  powerbi/      dashboard (.pbix) and theme (traffic_theme.json)
  images/       dashboard screenshots
  README.md
```

## How to run

1. Download the CSV from NYC Open Data and save it as `collisions.csv` in the project folder (the file is too large for GitHub).
2. Run the notebooks in order: explore, clean, load.
3. In MySQL, run the table, index and view scripts in `sql/`. Set your own MySQL user and password in the load notebook (do not commit passwords).
4. Open `powerbi/collisions.pbix` in Power BI Desktop (free from Microsoft) and refresh the data source to point to your MySQL server.

## What I learned

- Profiling raw data before cleaning, and documenting every cleaning decision.
- Reconciling counts at each stage so the final dashboard can be trusted.
- Data modelling with a date table, DAX measures and dynamic text.
- Designing a dashboard for non-technical users, and showing data gaps openly.
- report_image (https://github.com/Ronan-112/nyc-collisions-powerbi-dashboard/blob/main/Report.png)

## Author

**Roshan Shaw**, final-year B.Tech (IT), Kolkata
[LinkedIn](#) | [GitHub](#) | roshanshaw94770@gmail.com
