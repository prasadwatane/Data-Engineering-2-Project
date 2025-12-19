# Data-Engineering-2-Project
Strategy-driven analysis using Google Cloud Platform to turn NYC taxi trip records into insights + revenue forecasting.
# NYC Taxi Analytics (GCP)

Strategy-driven analysis using Google Cloud Platform to turn NYC taxi trip records into insights + revenue forecasting.

## Problem
NYC taxi stakeholders often lack visibility into performance drivers, leading to inefficiencies like deadheading and missed peak-demand opportunities. This project transforms raw trip records into actionable intelligence for revenue and operations. (Slide 2)

## Dataset
Source: NYC Taxi (TLC) Trip Record Data portal (NYC.gov). Monthly trip files for Yellow/Green taxis (PARQUET) containing timestamps, pickup/dropoff zone IDs, distance, passenger count, payment info (payment type, rate code), and fare components (fare, tip, tolls, surcharges, total amount). (Slide 3)

## Business questions
- Best time for drivers to be on the road?
- How much does congestion hurt speed/efficiency?
- Most popular pickup spots?
- Cash vs Card split?
- Revenue mix: many short cheap trips vs fewer long expensive trips? (Slide 4)

## Architecture
![Architecture](docs/architecture/architecture.png)

High-level flow (Slide 5):
1) Ingestion: API + Cloud Scheduler (monthly) pulls NYC TLC monthly parquet.
2) Cloud Run: splits the monthly file into weekly chunks (Week1–Week4) and stores in Cloud Storage.
3) Pub/Sub triggers processing events.
4) Cloud Functions runs transformation script (cleaning + feature creation) and loads to BigQuery.
5) BigQuery tables: `nyc_taxi_cleaned_data`, `actual_revenue`, `forecast_revenue`.
6) BigQuery ML: Linear Regression revenue forecasting.
7) Power BI dashboard connects to BigQuery for reporting.

## How to run
- Deploy Cloud Run splitter (see `cloud/cloud_run_splitter/README.md`)
- Deploy Cloud Function transformer (see `cloud/cloud_functions_transform/README.md`)
- Run SQL in `sql/` in order (01 → 05)
- Connect Power BI using `powerbi/README.md`


nyc-taxi-analytics-gcp/
├─ README.md
├─ docs/
│  ├─ architecture/
│  │  ├─ architecture.png        # export slide-5 image
│  │  └─ architecture.md         # explanation of each box/arrow
│  ├─ dataset.md                 # dataset + schema notes
│  └─ decisions.md               # partitioning/clustering + model choice notes
├─ sql/
│  ├─ 01_create_tables.sql       # partitioned+clustered tables
│  ├─ 02_cleaning.sql            # cleaning rules
│  ├─ 03_hourly_revenue.sql      # hourly aggregation
│  ├─ 04_lr_train_predict.sql    # linear regression training + next-month forecast
│  └─ 05_dashboard_views.sql     # views for Power BI
├─ notebooks/
│  └─ Untitled6.ipynb            # your exploration notebook
└─ powerbi/
   └─ README.md                  # connect Power BI -> BigQuery instructions

