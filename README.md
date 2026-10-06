# Netflix Movies & Shows Analysis Dashboard

### Dashboard Link: https://app.powerbi.com/links/FBp7zF_U0p?ctid=500356b3-ac75-4410-bd8f-decd38beb5a2&pbi_source=linkShare

**Tech stack:** Power Query | Power BI Desktop | DAX | Power BI Service

---

## Problem Statement

This project is based on a "Cinematic Data Dive" case study. The scenario: a media analytics firm is working with an international OTT platform to analyse how its movie and show content has performed and been distributed over time. The goal is to find patterns in audience engagement, content runtime and age-based classification, so the platform can make better content acquisition and production decisions.

The dashboard turns IMDb data on Netflix movies and shows (covering roughly the last 70 years) into simple, interactive visuals for stakeholders.

**Questions the dashboard answers:**
- How many titles are there, and how do average IMDb score, runtime and votes look overall?
- Which titles get the most audience engagement (votes x score)?
- How have releases, votes and ratings changed across release years?
- How do runtime and age certification relate to audience engagement?
- Do movies and shows differ in rating and runtime?

---

## Dataset

- **Source:** Course-provided dataset (Coding Ninjas DIY Activity: *Cinematic Data Dive*), containing IMDb data on Netflix movies and shows
- **Table name:** `Netflix TV Shows and Movies`
- **Records:** 5,283 (movies and shows)
- **Coverage:** Release years 1953 to 2022 (some years are missing from the data, for example 1955 and 1957)

| Column | Description |
|---|---|
| `id` | Unique ID of each title, used for distinct counts (for example, number of titles released in a year) |
| `title` | Name of the movie or show |
| `type` | `MOVIE` or `SHOW` |
| `release_year` | Year of release |
| `description` | Short description of the title |
| `age_certification` | Age rating (for example R, PG-13, TV-MA). Many values are blank and were left as is, because there is no reliable way to fill them |
| `runtime` | Runtime in minutes |
| `imdb_score` | IMDb rating of the title |
| `imdb_votes` | Number of IMDb votes. Titles with very few votes have less reliable scores |

**Columns added during the project:**

| Column | Created in | Description |
|---|---|---|
| `Vote X Score` | Power Query | `imdb_score` x `imdb_votes` (the "Rating*Votes" engagement measure) |
| `runtime_hrs` | DAX | Runtime converted from minutes to hours |
| `Norm_Score` | DAX | IMDb score scaled to a 0 to 1 range |
| `Norm_Vote` | DAX | Votes as a share of the highest vote count in the data |
| `Norm_Rating` | DAX | `Norm_Score` x `Norm_Vote` |

---

## Architecture / Data Flow

```
Dataset file  -->  Power Query Editor (transform)  -->  Power BI Desktop (DAX + report)  -->  Power BI Service
```

---

## Steps Followed

### Part 1: Power Query (Import and Transform)
- **Step 1:** Downloaded the dataset and imported it into Power BI Desktop using **Transform Data**, which opens the Power Query Editor.
- **Step 2:** Checked the data: runtime is in minutes, some release years are missing, and many age certifications are blank.
- **Step 3:** Created a new column `Vote X Score` = `imdb_score` x `imdb_votes`.
- **Step 4:** Loaded the transformed data into Power BI.

### Part 2: DAX (Columns and Measures)
- **Step 5:** Created the calculated columns `runtime_hrs`, `Norm_Score`, `Norm_Vote` and `Norm_Rating`.
- **Step 6:** Created a **Measure Table** with five measures: `Avg_Runtime`, `Avg_Score`, `Total Title`, `Total Vote X Vote` and `Total Votes`.

### Part 3: Power BI (Report Building)
- **Step 7:** Added slicers: `runtime_hrs` (Between), `release_year` (Tiles or Between range), `type` (Dropdown or list) and `age_certification`.
- **Step 8:** Built the **3-page report** with a Netflix-themed dark and red design (details in the Dashboard Snapshots section below).
- **Step 9:** Published the report to Power BI Service.

---

## DAX

### Calculated column
```DAX
runtime_hrs = DIVIDE ( 'Netflix TV Shows and Movies'[runtime], 60 )
```

```DAX
Norm_Score = 'Netflix TV Shows and Movies'[imdb_score] / 10
```

```DAX
Norm_Vote =
'Netflix TV Shows and Movies'[imdb_votes]
    / CALCULATE (
        MAX ( 'Netflix TV Shows and Movies'[imdb_votes] ),
        ALL ( 'Netflix TV Shows and Movies' )
    )
```

```DAX
Norm_Rating = 'Netflix TV Shows and Movies'[Norm_Score] * 'Netflix TV Shows and Movies'[Norm_Vote]
```

### Measures (stored in the Measure Table)
```DAX
Avg_Runtime = AVERAGE ( 'Netflix TV Shows and Movies'[runtime] )
```

```DAX
Avg_Score = AVERAGE ( 'Netflix TV Shows and Movies'[imdb_score] )
```

```DAX
Total Title = DISTINCTCOUNT ( 'Netflix TV Shows and Movies'[id.1] )
```

```DAX
Total Vote X Vote = SUM ( 'Netflix TV Shows and Movies'[Vote X Score] )
```

```DAX
Total Votes = SUM ( 'Netflix TV Shows and Movies'[imdb_votes] )
```

| Measure | What it shows |
|---|---|
| `Avg_Runtime` | Average runtime of titles (minutes) |
| `Avg_Score` | Average IMDb score |
| `Total Title` | Number of distinct titles |
| `Total Vote X Vote` | Total engagement (sum of score x votes) |
| `Total Votes` | Total IMDb votes |

---

## Dashboard Snapshots

### Page 1: Votes and Engagement
<img width="1482" height="732" alt="Page 1 - Votes and Engagement" src="https://github.com/user-attachments/assets/1e8a6669-49e5-4c29-9a54-876977924f6d" />

### Page 2: Scores, Runtime and Release Trend
<img width="1477" height="730" alt="Page 2 - Scores, Runtime and Release Trend" src="https://github.com/user-attachments/assets/3ddbf936-4874-48a7-8d4b-c9b41b66a23d" />

### Page 3: Runtime and Age Certification
<img width="1482" height="732" alt="Page 3 - Runtime and Age Certification" src="https://github.com/user-attachments/assets/2a705ef5-6c18-4cf2-96b0-d3a129c7d75c" />

---

## Insights

### 1. Page 1: Votes and Engagement

**Visuals:** Total Votes by release_year (line), Total Vote X Vote by title (bar), Sum of imdb_votes and Avg_Score by release_year (combo chart). Slicers: release_year, type, age_certification.

**Top 5 titles by Total Vote X Vote (as filtered on this page)**

| Rank | Title | Vote X Vote |
|---|---|---|
| 1 | Breaking Bad | 16.4M |
| 2 | Stranger Things | 8.6M |
| 3 | The Walking Dead | 7.8M |
| 4 | Black Mirror | 4.5M |
| 5 | House of Cards | 4.3M |

**Takeaways:**
- Breaking Bad leads by a wide margin, with almost double the engagement of the next title.
- Total votes rise sharply from the mid-2000s and peak at around 3M+ in the late 2010s. The drop at the end of the chart reflects recent releases that have had less time to collect votes.
- Average IMDb score is highest for older titles (above 8) and trends down to about 6.5 for the most recent years, as many more titles are released.

### 2. Page 2: Scores, Runtime and Release Trend

**Visuals:** Avg_Score and Total Title by type and age_certification (scatter), Avg_Runtime by title (line), Count of title by release_year (bar). Slicers: type, release_year.

**Takeaways:**
- Shows (TV-14, TV-MA, TV-PG and so on) have average scores of about 6.5 to 7.3, while movie certifications sit lower, at about 6.2 to 6.4.
- The longest titles run for 224 to 225 minutes (for example *A Lion in the Hole* and *Lagaan: Once Upon a Time in India*), far above the overall average runtime of about 78 minutes.
- The number of titles per release year grows steadily after 2010 and peaks at about 749 titles around 2019. The 2022 bar (182) is lower because that year is incomplete in the data.

### 3. Page 3: Runtime and Age Certification

**Visuals:** Average of Vote X Score by runtime (area), Sum of Vote X Score and Average runtime_hrs by age_certification (combo chart), ID count by Release Year (donut), Count of id by age_certification (bar), First title card and description multi-row card. Slicers: runtime_hrs (Between), type, release_year (Tiles).

**Takeaways:**
- Engagement is low for most runtimes and shows spikes at about 150 to 235 minutes, with the highest peak (4.4M) at around 190 minutes. This is driven by a few very popular long titles.
- R and PG-13 titles have the highest total Vote X Score, ahead of PG, G and NC-17.
- A large share of titles has a blank age certification (about 2K, the biggest bar), so certification-based conclusions should be read with care.
- In the release-year donut, 2019 has the largest share (13.77%), followed by 2018, 2021 and 2020.

### 4. Overall Takeaways
- A small number of well-known shows account for a very large share of audience engagement.
- Content volume has grown quickly since about 2015, while average scores have drifted down.
- Longer titles show engagement spikes, but these come from a few outliers rather than a general pattern.
- Missing age certifications limit how much can be said about audience classification.

---

## Recommendations

1. Invest in shows similar to the top engagement titles, since shows score higher on average than movies.
2. Review the quality of recent releases, because average scores have fallen as volume has grown.
3. Treat long-runtime outliers carefully when planning content length, as high engagement there comes from a few titles.
4. Fill in or collect the missing age certifications so the classification analysis is more reliable.
5. Add genre information (for example by extracting keywords from the `description` column) for deeper analysis.

---

## Tools Used

- Power Query Editor
- Power BI Desktop
- DAX
- Power BI Service

## Author

**Shaswat Kumar** | [LinkedIn](https://www.linkedin.com/in/shaswat-kumar07) | [shaswat844@gmail.com](mailto:shaswat844@gmail.com)
