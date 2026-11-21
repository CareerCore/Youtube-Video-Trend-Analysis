# What Makes a YouTube Video Trend? A Data Story for Creators

## Project Overview

This project explores daily YouTube trending-video records to understand which categories and regions appear most often, how views and engagement rates differ, how long videos tend to be published before appearing on the trending list, and whether pre-performance metadata can help benchmark high engagement.

The analysis is designed as a creator-friendly data story. It identifies patterns in the selected trending-video sample; it does **not** claim that any single title, publishing time, or metadata formula guarantees virality.

## Client Brief

The project was framed around a practical content-strategy question: **What characteristics are associated with videos appearing on YouTube's trending list, and what can creators learn from those patterns?**

The analysis addresses the following questions:

- Which video categories appear most frequently in the selected regional trending records?
- How do views and engagement rates differ between the United States and India samples?
- How do views, likes, and comments relate to one another?
- What publishing-time and title-length patterns appear in the records?
- Can metadata available before performance is observed provide a useful benchmark for identifying records with above-median like rates?

## Dataset

The analysis uses two regional CSV files:

- `USvideos.csv` — United States trending-video records
- `INvideos.csv` — India trending-video records

Both files are stored in the repository's `data/` directory. The notebook combines the regional files and adds a `region` column to distinguish them.

| Region | Records | Unique videos |
|---|---:|---:|
| India (IN) | 37,352 | 16,307 |
| United States (US) | 40,949 | 6,351 |
| **Combined** | **78,301** | **22,427** |

The dataset contains repeated daily observations for videos that remain on the trending list. The notebook reports **4,311 duplicate rows** in the combined data. Therefore, record counts should not be interpreted as counts of distinct videos.

## Tools & Technologies

- **Python** – Analysis and data processing
- **Pandas** – Data loading, cleaning, transformation, and aggregation
- **NumPy** – Numerical operations
- **Matplotlib** – Charts and plots
- **Seaborn** – Statistical visualizations
- **Scikit-learn** – Preprocessing, logistic regression, and model evaluation
- **Jupyter Notebook** – Notebook execution environment

## Project Workflow

### 1. Data loading

The notebook reads `USvideos.csv` and `INvideos.csv` from the repository's `data/` directory, uses `latin1` encoding, adds a regional identifier, and combines the available data.

### 2. Data validation and feature engineering

The notebook checks the expected dataset schema, parses trending and publishing dates, and creates additional variables, including:

- `days_to_trend` – Approximate days between publication and the recorded trending date
- `title_chars` and `title_words` – Title length measures
- `tag_count` – Number of tags
- `publish_hour` – Publishing hour in UTC
- `like_rate` – Likes divided by views
- `comment_rate` – Comments divided by views

The notebook also checks duplicate rows and summarizes key numerical variables.

### 3. Regional and category analysis

The analysis compares record counts, unique videos, median views, and median like rates for India and the United States. It also identifies frequently appearing category IDs in each region.

Category IDs are retained rather than assigned category names because category mappings can vary by region.

### 4. Engagement analysis

The notebook examines the relationship between views and likes and compares like-rate distributions across regions. It also calculates median and mean like rates for each regional sample.

### 5. Publishing time and title patterns

The analysis explores trending records by UTC publishing hour, the approximate time between publication and trending-list appearance, and title-length statistics by region.

These are descriptive associations, not evidence that changing a publishing hour or title length will cause a video to trend.

### 6. High-engagement benchmark model

A logistic regression pipeline benchmarks whether a record's like rate is at or above the **training-set median**.

The model uses metadata intended to be available before performance is observed:

- Region
- Category ID
- Publishing hour
- Title character count
- Title word count
- Tag count
- Whether comments are disabled

Performance variables such as views, likes, dislikes, comment count, and target-derived rates are excluded from the model features to reduce target leakage. The notebook uses a train/test split, preprocessing pipelines, one-hot encoding, imputation, and feature scaling.

## Key Findings

### Regional differences

| Metric | India (IN) | United States (US) |
|---|---:|---:|
| Trending records | 37,352 | 40,949 |
| Unique videos | 16,307 | 6,351 |
| Median views | 304,586 | 681,861 |
| Median like rate | 0.0095 (0.95%) | 0.0283 (2.83%) |
| Mean like rate | 0.0220 (2.20%) | 0.0344 (3.44%) |
| Median title length | 83 characters | 47 characters |

In this dataset sample, the United States records have higher median views and like rates, while the India records contain more unique videos. The median title length also differs between the two regional samples.

### Frequently appearing category IDs

The most frequent category IDs differ in volume by region:

- **India:** category ID `24` has 16,712 records, followed by category ID `25` with 5,241.
- **United States:** category ID `24` has 9,964 records, followed by category ID `10` with 6,472.

These are category IDs, not category names.

### Engagement and model benchmark

The training-set median like-rate cutoff is **0.02011 (about 2.01%)**.

On the held-out test set, the logistic regression benchmark achieved:

| Evaluation metric | Result |
|---|---:|
| ROC AUC | 0.830 |
| Balanced accuracy | 0.757 |
| Test accuracy | approximately 0.76 |

The model's test-set precision and recall were:

| Class | Precision | Recall | F1-score |
|---|---:|---:|---:|
| Below median like rate | 0.77 | 0.73 | 0.75 |
| At/above median like rate | 0.75 | 0.78 | 0.76 |

This indicates that the selected metadata contains useful signal for distinguishing above- versus below-median like-rate records in this dataset. It is a benchmark model, not a replica of YouTube's recommendation or trending systems, and it does not establish causation.

## Creator Takeaways

- Compare engagement rates as well as raw view counts; raw counts are strongly influenced by audience size and channel reach.
- Examine category and regional patterns in the context of the intended audience.
- Treat title length and publishing-time patterns as observations to investigate, not guaranteed growth tactics.
- Use pre-performance metadata as a benchmarking aid, not as a promise that a video will trend.

## Limitations

- The records come from 2017–2018 collection windows and selected regions; they do not represent all YouTube uploads or current platform behavior.
- The dataset contains repeated daily observations of videos that remained on the trending list.
- Trending-list data are already a selected sample, not a random sample of all videos.
- The `days_to_trend` measure is approximate because the daily trending date is not an exact recommendation timestamp.
- Engagement rates can be affected by audience mix, video format, channel size, and calls to action.
- The analysis is descriptive and non-causal. No title length, category, tag count, or publishing hour is shown to cause virality.

## Repository Structure

The repository is organized as follows:

```text
youtube-video-trend-analysis/
├── README.md
├── notebooks/
│   └── what-makes-a-youtube-video-trend.ipynb
└── data/
    ├── USvideos.csv
    └── INvideos.csv
```

Keep both CSV files in the `data/` folder so the notebook can load them from the same relative location. If either file is too large for a regular GitHub upload, use Git Large File Storage (Git LFS) or provide an alternative download location.

## How to Run

1. Clone the repository:

   ```bash
   git clone YOUR_GITHUB_REPOSITORY_URL
   cd youtube-video-trend-analysis
   ```

2. Install the Python dependencies:

   ```bash
   pip install numpy pandas matplotlib seaborn scikit-learn jupyter
   ```

3. Make sure `USvideos.csv` and `INvideos.csv` are saved in the repository's `data/` folder.

4. In the notebook, set the data paths to:

   ```python
   us_path = "../data/USvideos.csv"
   in_path = "../data/INvideos.csv"
   ```

   These relative paths assume the notebook is inside the `notebooks/` folder. If you keep the notebook in the repository root, use `data/USvideos.csv` and `data/INvideos.csv` instead.

5. Launch Jupyter:

   ```bash
   jupyter notebook
   ```

6. Open the notebook and run the cells.

## Project Deliverables

- Combined regional trending-video analysis
- Data validation and feature engineering
- Regional, category, engagement, timing, and title-pattern exploration
- Data visualizations
- Logistic regression high-engagement benchmark
- Creator-focused takeaways and documented limitations

## Project Context

This repository presents a client-style data analytics project, documenting the workflow from dataset preparation and exploratory analysis through model benchmarking and interpretation.
