# Spotify Ad Revenue and Financial Analytics 

## 🧭 Executive Summary 
<p>
This analysis evaluates Spotify's 2024 advertising revenue performance across content type, artist, and time, identifying key drivers of ad revenue growth and gross margin to inform strategic investment and promotional decisions that maximize long-term profitability.
</p>

---

### 💼 Business Problem
<p>
Spotify needs to improve ad revenue growth and profitability, but it is unclear which levers - content mix, stream volume, or artist investment, have the greatest impact on margins. Without understanding these drivers, the company cannot confidently decide whether to prioritize podcast vs music growth, or where to allocate promotional investment for maximum return.

</p>

---
## ❓ Analysis Questions 
<p>
  
#### Revenue & Engagement Questions

Which podcasts, tracks and artists drive the most revenue? 

Are podcasts more profitable than music? or Which content types have the highest gross margin? or What should Spotify invest more in?

How does user engagement (streams) impact revenue?

Which genres monetize best

Which content has highest gross margin and which content destroys margin?

#### Scenario & Forecasting Based Questions

What happens if streams increase 10–20%?

What is the impact of a 5–10% CPM change on revenue?

</p>

---

## 💻 Git File Structure Explained:

<strong>eda_notebook/modeling.ipynb</strong> : scenario model using the key revenue levers like stream volume and CPM, to project revenue uplift under different investment scenarios. 

<strong>eda_notebook/eda.ipynb</strong> : intial data exploration

<strong>extract/</strong> : using spotify web api to extract tables needed and dump then to Google Cloud Storage(GCS)

<strong>transform/dbt</strong> : Under this the model folder is divided into staging, intermediate(transformations) and marts(final dim/fct tables for reporting tool), test folder has custom test cases other than built in test cases in schema.yml(for each layer)

<strong>utils/</strong> : Common functions

---

## 🧠 Skills & Tech Stack
| Layer | Tool | Detail |
|-------|------|--------|
| Dataset | Spotify Web API | Tracks & podcasts - popularity, streams, impressions, CPM|
| Data Lake | Google Cloud Storage | Raw data store - staging area before loading into BigQuery |
| Warehouse | Google BigQuery | Cloud data warehouse - hosts raw, intermediate and mart tables |
| Transform | dbt | Staging · Intermediate · Marts · Seeds· Macros · Schema + custom tests |
| Analytics | Python | Revenue simulation · Scenario modeling |
| Visualization | Power BI + DAX | MoM% revenue · Dynamic min/max · Content type slicer · Scatter plot quadrants · Scenario projections |

---

## ⚙️ Methodology 

<h3>1. Dataset Used:</h3> 
Spotify Web API - https://developer.spotify.com/documentation/web-api/

Note: 
- Since Spotify’s public API does not provide stream counts or ad monetization metrics, I constructed a revenue simulation model using track popularity as a proxy for demand. Streams were estimated as popularity × 1000, impressions per stream were randomized between 1–3 based on typical ad loads, and CPM was assumed at $5 for tracks and $10 for podcasts based on industry benchmarks.

- I simulated the user data table and since I used a constant CPM throught 2024 and since stream impressions are less and dataset size is small, hence overall revenue will be a small amnt

<h3>2. Architecture Diagram:</h3>
<img width="696" height="1106" alt="Olist E-commerce Data Pipeline Architecture - visual selection (2)" src="https://github.com/user-attachments/assets/58e75b41-91e4-4576-90ee-95a81410b152" />

<h3>dbt Implementation:</h3>

- Staging layer - standardized raw BigQuery tables through column renaming creating clean reusable source models
- Intermediate layer - applied business logic and joins across staging models to create enriched datasets for downstream consumption
- Marts layer - built production-ready dimensional models (dim/fct tables) consumed directly by Power BI for reporting
- Seeds - used for static reference data (e.g. CPM rates by content type) loaded directly into BigQuery via dbt
- Macros - wrote reusable SQL macros to standardize repetitive transformation logic across models
- Testing - implemented schema tests (not_null, unique, accepted_values) for each layer and custom tests to ensure data quality and pipeline reliability

<h3>Lineage graph of one table (fct_podcast_streams) using "dbt docs serve"</h3>
<img width="1811" height="737" alt="dbt generate" src="https://github.com/user-attachments/assets/5cdccc8b-093f-45ef-a621-c6e1148607a7" />

<h3>Power BI & Advanced Analytics:</h3>

- Built advanced DAX measures including MoM% revenue change, dynamic min/max data point identification and a calculated sort column for abbreviated month sorting(on chart)
- Developed a driver-based scenario model projecting revenue uplift under 10-20% stream growth and 5-10% CPM change scenarios — providing actionable forecasting without reliance on black-box statistical models
- Designed an interactive dashboard with content type slicer enabling dynamic filtering across all visuals

<h3>3. Google Cloud Storage & Google Big Query</h3>

<img width="1161" height="490" alt="gcs" src="https://github.com/user-attachments/assets/33f8568d-e52e-469e-a4e6-9c60aa02bea9" />

<img width="978" height="829" alt="google bigquery" src="https://github.com/user-attachments/assets/45ad3974-8624-4480-a837-950d6aead859" />

---

## 📈 Results

Power BI Report Snapshot:

<img width="1419" height="804" alt="Screenshot 2026-03-20 at 1 48 06 PM" src="https://github.com/user-attachments/assets/e01ed52e-fc65-4d96-9f24-71a2718a3f08" />


Next 2 Charts are - to see which content names are performing well/not performing well by revenue and gross profit.

**Filtered by podcast:**

<img width="1420" height="780" alt="Screenshot 2026-03-26 at 2 31 06 PM" src="https://github.com/user-attachments/assets/b74bf7f2-8143-4d90-baa8-41dcf2ce4bc4" />


**Filtered by music:**

<img width="1425" height="785" alt="Screenshot 2026-03-26 at 2 30 34 PM" src="https://github.com/user-attachments/assets/6921a8ae-8c5b-449c-8fd7-ec36e8b9f902" />


---

## 📊 Business Insights & Recommendations

<!-- <img width="1375" height="420" alt="Screenshot 2026-02-24 at 1 49 15 PM" src="https://github.com/user-attachments/assets/4c1c8aba-c033-4d1b-a9d6-d0bc952db00f" /> -->

Identified that top revenue-generating content also drove the highest gross profit, highlighting a strong correlation between revenue and margin performance

| Finding | Business Impact | Recommendation | 
|---------|----------------|----------------|
| Business & Comedy podcasts drive ~64% of gross margin | Profitability concentrated in 2 genres | Prioritize promotion in January when ad revenue peaks |
| B.o.B outperforms next artist by 2.7x ($35 vs $13) | Single artist drives music ad revenue | Prioritize catalog licensing + playlist placement. A 20% increase in streams through playlist promotion could yield $42 which is an additional $7 uplift. With CPM fixed at $5, every 1,000 additional streams generates $5 in incremental revenue for tracks.| 
| February lowest ad revenue ($614) — MoM decline 8.55% | Predictable seasonal revenue gap | Run curated playlist promotions in Feb. Promote high-margin content types like Business & Comedy podcasts in February to offset seasonal stream decline| 
| "Rocky Bhai" music and "PNeznation live with Professor Nez" podcast show lowest gross margin | Low-margin content destroys ROI | Reallocate budget toward "Technology" podcast and "Pop" music which drive the highest gross margin| 

> * Revenue figures based on simulated data. CPM assumed $5 tracks, $10 podcasts. Dollar amounts illustrative, not actual Spotify revenue.*
> * Calculation for tracks,  based on fixed CPM of $5 for music:
    Assumption: 20% stream increase, Current B.o.B ad Revenue = 35, Projected B.o.B ad Revenue = 35 × 1.20 = 42
    Uplift = 42 - 35 = $7 (20%)*

---

## ⚡ Future Scope - Scalable Implementation 

<ul>
<li> <b>Automation & Orchestration:</b>: 
Apache Airflow can be used to schedule and orchestrate the end-to-end ELT pipeline by automating data ingestion from Spotify API, GCS uploads, BigQuery loads and dbt model runs on a daily/weekly basis, replacing manual execution with a fully automated workflow.</li>

<li> <b>Real-time streaming</b>:
Replace batch ingestion with real-time streaming using Google Pub/Sub or Kafka for live ad revenue monitoring</li>
</ul>




