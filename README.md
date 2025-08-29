# Netflix-Power-BI-Dashboard
Interactive Power BI dashboard analyzing Netflix’s catalog (2008–2021): global map, ratings breakdown, YoY growth, monthly trends, and content-type drilldowns — built with Power Query + DAX.

![Preview](assets/Netflix_Dashboard.jpg)

## Table of Contents

1. [Overview](#overview)
2. [Key Features](#key-features)
3. [Data Source](#data-source)
4. [Folder Structure](#folder-structure)
5. [Getting Started](#getting-started)
6. [Build Steps (Step‑by‑Step)](#build-steps-step-by-step)
7. [DAX Measures & Calculated Columns](#dax-measures--calculated-columns)
8. [Dashboard Walkthrough](#dashboard-walkthrough)
9. [Insights (from this build)](#insights-from-this-build)
10. [How to Use / Interact](#how-to-use--interact)
11. [Export / Share](#export--share)
12. [Contributing](#contributing)
13. [License](#license)
14. [Credits](#credits)
15. [Contact](#contact)

---

## Overview

This project contains an interactive **Power BI** dashboard that explores the **Netflix Movies & TV Shows** catalog across years and regions. It provides a quick view of total titles, content-type split, rating distribution, and trends over time.

**Main questions answered**

* How many titles are available overall and by content type (Movie vs TV Show)?
* Which ratings (TV‑MA, TV‑14, etc.) dominate the catalog?
* How has the content grown year‑over‑year and month‑to‑date?
* Which countries contribute content on Netflix?

---

## Key Features

* **KPI Cards:** Total Titles, Movies, TV Shows.
* **Global Map:** Country‑wise distribution (bubble/pie markers).
* **Donut Chart:** Share by show type (Movies vs TV Shows).
* **Bar Chart:** Titles by rating (TV‑MA, TV‑14, PG‑13, etc.).
* **Combo Chart:** Titles per year with **YoY %** line.
* **Monthly Trend:** Titles **MTD** line chart.
* **Slicers:** Year range (2008–2021) and Content Type (Movie/TV Show).

---

## Data Source

Use the commonly‑available **Netflix Movies and TV Shows** dataset (CSV). You can download it from public sources (e.g., Kaggle). Place the file in `data/` with the name `netflix_titles.csv`.

**Typical columns**: `show_id, type, title, director, cast, country, date_added, release_year, rating, duration, listed_in, description`.


## Build Steps (Step‑by‑Step)

**Step 1 — Load data**
Home ➜ *Get Data* ➜ *Text/CSV* ➜ select `data/netflix_titles.csv` ➜ *Transform Data*.

**Step 2 — Clean & transform (Power Query)**

* Trim/clean text columns (director, cast, country, rating).
* Parse `date_added` as *Date*.
* Create a **Calendar** table later in DAX (see below) or in Power Query if preferred.
* Keep only columns you need for visuals to keep the model lean.

**Step 3 — Model**

* Ensure a one‑to‑many relationship from `Calendar[Date]` to `Titles[date_added]` (or using `release_year` if your logic uses year of release instead of `date_added`).

**Step 4 — DAX measures**
Create the measures in the **DAX** section below for KPIs, YoY%, and MTD.

**Step 5 — Visuals**

* **KPI Cards:** `Total Titles`, `Movies`, `TV Shows`.
* **Map:** Country as Location, `Total Titles` as Size. (Optional pie markers by Type.)
* **Donut:** `Total Titles` by `type`.
* **Bar (horizontal):** `rating` on axis, `Total Titles` as value.
* **Combo (clustered column + line):** `Year` on X; columns = `Total Titles`; line = `Titles YoY %`.
* **Line (Monthly):** `MonthName` on X; `Titles MTD` as value.
  👉 Make sure **MonthName** is **sorted by MonthNum** (Column tools ➜ *Sort by Column*).

**Step 6 — Slicers**

* Year (between/slider): use `Calendar[Year]` or `date_added` years.
* Buttons for Movie / TV Show (Format ➜ *Buttons* or use a slicer on `type`).

**Step 7 — Format**

* Apply a dark theme and Netflix‑style red accents.
* Add a top bar with icons (Home, Filter) for a polished look.

---

## DAX Measures & Calculated Columns

> Adjust table/column names (`Titles`, `Calendar`) to match your model.

**Calendar table**

```DAX
Calendar = CALENDAR ( MIN ( Titles[date_added] ), MAX ( Titles[date_added] ) )
Year = YEAR ( 'Calendar'[Date] )
MonthNum = MONTH ( 'Calendar'[Date] )
MonthName = FORMAT ( 'Calendar'[Date], "mmmm" )
```

Then select **MonthName** ➜ *Column tools* ➜ **Sort by Column** ➜ **MonthNum**.

**Core measures**

```DAX
Total Titles = COUNTROWS ( Titles )
Movies = CALCULATE ( [Total Titles], Titles[type] = "Movie" )
TV Shows = CALCULATE ( [Total Titles], Titles[type] = "TV Show" )
```

**YoY %** (by Year)

```DAX
Titles YoY % =
VAR Prev = CALCULATE ( [Total Titles], SAMEPERIODLASTYEAR ( 'Calendar'[Date] ) )
RETURN DIVIDE ( [Total Titles] - Prev, Prev )
```

**Month‑to‑Date**

```DAX
Titles MTD = CALCULATE ( [Total Titles], DATESMTD ( 'Calendar'[Date] ) )
```

**Optional helpers**

```DAX
Titles by Rating = [Total Titles]
Titles by Country = [Total Titles]
```

---

## Dashboard Walkthrough

* **Top KPIs:** High‑level counts for Total, Movies, TV Shows.
* **Map:** Visualizes geographic spread of content on Netflix.
* **Show Type Donut:** Shares of Movies vs TV Shows.
* **Ratings Bar:** Which maturity ratings dominate the catalog.
* **Titles & YoY %:** Growth over time with YoY change.
* **Titles & MTD:** Seasonality across months.

---

## Insights (from this build)

* Example counts from the current file: **8,807** total titles; **6,131** Movies; **2,676** TV Shows.
* TV content makes up \~**30%** and Movies \~**70%** in this snapshot.
* **TV‑MA** and **TV‑14** are the most common ratings in the dataset used here.

> Numbers can vary based on the exact dataset snapshot and filters.

---

## How to Use / Interact

* Use the **year slider** to focus on a specific range.
* Toggle **Movie / TV Show** buttons (or slicer) to filter visuals.
* Hover on map bubbles to see country‑level counts.
* Scroll the Ratings bar chart to view all categories if the list is long.


## Credits

* Dataset: Public Netflix catalog dataset (CSV).
* Tools: **Power BI Desktop**, **DAX**, **Power Query**.


## Contact

**Md Shahid Afridi**

* GitHub: [https://github.com/](https://github.com/msafridi1999/Netflix-Power-BI-Dashboard/edit/main/README.md)
* LinkedIn: [https://www.linkedin.com/in/](https://www.linkedin.com/in/mdshahidafridi-data)
* Email:mdshahidafridi9060@gmail.com

