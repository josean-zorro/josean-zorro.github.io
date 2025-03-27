---
title: Taiwan Real Estate
---

## 1. Project Overview:

### Goal and Purpose:
The primary goal of this project is to showcase my technical proficiency in data engineering and business intelligence (BI), with a particular focus on modern ELT (Extract, Load, Transform) practices, dimensional modeling, and the creation of insightful visualizations. This project serves as a practical demonstration of my ability to deliver an end-to-end analytics solution.

### Scope:
The project includes the following key activities:
+ Web Scrapping: Scrapping data from [Taiwan Real Estate Transaction Price Registration System](https://plvr.land.moi.gov.tw/DownloadOpenData) 
+ Extract and Load: Stored Scraped data in structured storage.
+ Data Transformation: Cleaning, modeling, and aggregating the data using ELT workflows to prepare it for analytical querying.
+ Semantic Dataset: Designing a clear, easy-to-query dimensional model and a semantic (metrics) layer to facilitate meaningful analytics.
+ Dashboard Design and Implementation: Creating interactive dashboards guided by simulated stakeholder interactions (via ChatGPT), ensuring relevance, clarity, and alignment with realistic business requirements and user scenarios.



## 2. Data Sources and Infrastructure

### Data Sources
+ [city_code.csv](https://github.com/josean-zorro/tw_real_estate/blob/main/transform/seeds/taiwan_city_code.csv)
	+ Manually collected through google search and wikipedia
+  [Taiwan Real Estate Transaction Price Registration System](https://plvr.land.moi.gov.tw/DownloadOpenData) 
	+ land, building, and parking space transactions
	+ Pre-sale house (TBD)

### Tools and Technologies

- **Programming Language:**   ![Python](https://img.shields.io/badge/-Python-3776AB?style=flat&logo=python&logoColor=white)

- **Cloud Storage:**   ![Google Cloud Storage](https://img.shields.io/badge/-Google%20Cloud%20Storage-4285F4?style=flat&logo=googlecloud&logoColor=white)

- **Data Warehouse:**  ![BigQuery](https://img.shields.io/badge/-BigQuery-4285F4?style=flat&logo=googlebigquery&logoColor=white)

- **Extract and Load:**  ![Meltano](https://img.shields.io/badge/-Meltano-4C2E91?style=flat&logo=meltano&logoColor=white)

- **Transformation:**  ![dbt](https://img.shields.io/badge/-dbt-FF694B?style=flat&logo=dbt&logoColor=white)

- **CI/CD:**    ![GitHub Actions](https://img.shields.io/badge/-GitHub%20Actions-2088FF?style=flat&logo=githubactions&logoColor=white)

- **Semantic Layer (Data Mart):**  ![Power BI](https://img.shields.io/badge/-Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)

- **Visualization:**  ![Power BI](https://img.shields.io/badge/-Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)

### Infrastructure Overview

Web scraping → Raw data storage in Google Cloud Storage → Data ingestion and loading into BigQuery using Meltano → Data transformation and modeling in BigQuery using dbt → Semantic dataset creation in Power BI → Interactive dashboards and analytics in Power BI.


## 3. Data Models & Transformations

### Data Mart & Data Modeling Description:
This project uses a dimensional modeling approach in data marts optimized for analytical queries. The schema shown as the follows:

```mermaid
erDiagram
  "MODEL.HOUSE_AND_RENTING.DIM_BUILDING_TYPE" {
    string building_type_id
    string building_type
    string building_type_origin
    timestamp last_batched_at
  }
  "MODEL.HOUSE_AND_RENTING.DIM_CITY_DISTRICT" {
    string city_county
    string city_id
    string city_district_surrogate_key
    string district_township
    timestamp last_batched_at
  }
  "MODEL.HOUSE_AND_RENTING.DIM_DATE" {
    date date_day
    date prior_date_day
    date next_date_day
    date prior_year_date_day
    date prior_year_over_year_date_day
    int64 day_of_week
    int64 day_of_week_iso
    string day_of_week_name
    string day_of_week_name_short
    int64 day_of_month
    int64 day_of_year
    date week_start_date
    date week_end_date
    date prior_year_week_start_date
    date prior_year_week_end_date
    int64 week_of_year
    date iso_week_start_date
    date iso_week_end_date
    date prior_year_iso_week_start_date
    date prior_year_iso_week_end_date
    int64 iso_week_of_year
    int64 prior_year_week_of_year
    int64 prior_year_iso_week_of_year
    int64 month_of_year
    string month_name
    string month_name_short
    date month_start_date
    date month_end_date
    date prior_year_month_start_date
    date prior_year_month_end_date
    int64 quarter_of_year
    date quarter_start_date
    date quarter_end_date
    int64 year_number
    date year_start_date
    date year_end_date
    string year_month_number
  }
  "MODEL.HOUSE_AND_RENTING.DIM_LAYOUT" {
    string number_of_bedrooms_categorized
    int64 building_number_of_bedrooms
    int64 building_number_of_living_rooms
    int64 building_number_of_bathrooms
    string building_number_of_partitions
    string layout_id
    timestamp last_batched_at
  }
  "MODEL.HOUSE_AND_RENTING.DIM_TRANSACTION_TYPE" {
    string transaction_type
  }
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" {
    string city_district_surrogate_key
    string layout_id
    string building_type_id
    string transaction_id
    date transaction_date
    string transaction_type
    string address_land_slot
    date construction_completion_date
    int64 number_of_lands
    int64 number_of_buildings
    int64 number_of_parking_spaces
    float64 land_total_tranferred_area_square_meter
    float64 building_transferred_area_square_meter
    float64 parking_transferred_area_square_meter
    float64 main_building_area_square_meter
    float64 auxiliary_building_area_square_meter
    float64 balcony_area_square_meter
    float64 total_price_ntd
    float64 price_per_square_meter
    float64 parking_total_price_ntd
    float64 building_price_per_square_meter
    float64 net_private_building_price_per_square_meter
    float64 net_exclusive_area_price_per_square_meter
    timestamp _sdc_batched_at
  }
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" }|--|| "MODEL.HOUSE_AND_RENTING.DIM_BUILDING_TYPE": building_type_id
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" }|--|| "MODEL.HOUSE_AND_RENTING.DIM_CITY_DISTRICT": city_district_surrogate_key
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" }|--|| "MODEL.HOUSE_AND_RENTING.DIM_LAYOUT": layout_id
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" }|--|| "MODEL.HOUSE_AND_RENTING.DIM_DATE": date_day--transaction_date
  "MODEL.HOUSE_AND_RENTING.FCT_REAL_ESTATE_TRANSACTION" }|--|| "MODEL.HOUSE_AND_RENTING.DIM_TRANSACTION_TYPE": transaction_type


```

### Transformation Logic:

Please refer to [dbt transformation logic](https://gallopgoose.com/tw_real_estate/#!/overview)


## 4. Data Quality & Testing

### Tests & Validation

Data quality is maintained through automated tests defined within dbt models, including:

#### Source Layer:
+ **Uniqueness tests**: Ensuring transaction IDs are unique.
+  **Non-null checks**: Ensuring critical fields (e.g., transaction dates, price values) contain no null values.
+ Pattern Checks: Ensuring critical fields (e.g., date, district) have the correct pattern

#### Data Mart Layer:
- **Uniqueness tests**: Ensuring transaction IDs are unique.
- **Non-null checks**: Ensuring critical fields (e.g., transaction dates, price values) contain no null values.
- **Referential integrity checks**: Confirming dimension keys in fact tables have valid dimension records.

These tests run automatically during each dbt deployment as part of CI/CD pipelines (GitHub Actions), ensuring data reliability and preventing issues from reaching the BI layer.

## 5. Semantic Dataset, BI & Visualization

### Semantic Dataset & Setup

A semantic model was developed within **Power BI**, connected directly to the well-transformed tables in **Google BigQuery**. This model defines key measures, fact, and dimension tables, and their relationships—enabling self-service analytics and consistent reporting logic across all visuals.

**Key features include:**
- Galaxy schema design with clearly defined fact and dimension tables.
- Centralized definitions for KPIs such as average price, price per square meter, and transaction counts.
- Built-in hierarchies for drill-downs (e.g., region → district).

**Notes:**
- All data transformations are performed using **dbt**.
- Minimal business logic is handled in Power BI’s **Power Query**, keeping the semantic layer clean and focused.
- - **Data refresh schedule**: Dashboards are refreshed regularly on the first Monday after the 1st, 11th, and 21st of each month. This aligns with the update frequency of the [Taiwan Real Estate Transaction Price Registration System](https://plvr.land.moi.gov.tw/DownloadOpenData) and considers limitations of the **Power BI Free version** (which lacks automatic scheduled refresh on cloud datasets).



### BI Tool & Setup

The dashboards were built using **Power BI** and connected directly to the semantic dataset, ensuring that KPIs across different reports / dashboards share with the same definitions.

DAG(TBD)

### Dashboard Catalog

+ [[Market Analysis for Investor]] (Click to view the dashboard)


- Market Analysis for Homebuyer (TBD)


## 6. Future Enhancement

+ A better schema design for dimension, especially building type and transaction types
+ Ingest pre-sales house data
+ Ingest rental Data
+ Martet Analysis for HomeBuyer
