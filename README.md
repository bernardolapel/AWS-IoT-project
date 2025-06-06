# AWS-IoT-project
# 🚜 Smart Agriculture Telemetry Platform on AWS

This project simulates off-highway equipment (e.g., tractors, harvesters) publishing real-time telemetry data to AWS IoT Core. The pipeline demonstrates end-to-end data engineering from IoT ingestion to analytics and machine learning using AWS Glue, Athena, QuickSight, and SageMaker.

---

## 📦 Features

- Simulates telemetry data (GPS, fuel level, engine temperature, etc.)
- Ingests data via AWS IoT Core using MQTT over WebSocket
- Stores data in Amazon S3 using IoT Rules
- Uses AWS Glue to catalog, transform, and structure data
- Queries structured data using Amazon Athena
- Visualizes insights using Amazon QuickSight
- Trains and deploys predictive models using Amazon SageMaker

---

## 🚀 Architecture Overview

```
[Simulated IoT Device]
        ↓
[AWS IoT Core MQTT Topic]
        ↓
[IoT Rule Action → Amazon S3]
        ↓
[Glue Crawler → Glue Data Catalog]
        ↓
[Glue ETL Job → Cleaned S3 Data]
        ↓
[Athena / QuickSight / SageMaker]
```

---

## 🧪 Step-by-Step Guide

### 1. Simulate Device Data
- Use Python script with AWSIoTPythonSDK
- Publish telemetry data to `farm/tractors/data`
- Use MQTT over WebSocket (port 443)

### 2. IoT Core Setup
- Create a Thing, certificate, and policy
- Subscribe to topic in **MQTT Test Client**
- Create an **IoT Rule**:
  - SQL: `SELECT * FROM 'farm/tractors/data'`
  - Action: Store to S3 (e.g., `tractor-data/${timestamp()}.json`)
  - IAM Role must have `s3:PutObject` permission

---

### 3. AWS Glue Setup

#### a. Create Glue Crawler
- Source: S3 bucket (e.g., `s3://tractor-data`)
- Output: Glue database (e.g., `tractor_telemetry_db`)
- Table name: e.g., `raw_tractor_data`
- Run crawler to generate schema

#### b. Create Glue ETL Job
- Transform raw JSON into structured format
- Convert timestamps, clean nulls, normalize schema
- Output to new S3 location (e.g., `s3://tractor-data/cleaned/`)
- Create new table `cleaned_tractor_data`

---

### 4. Amazon Athena

- Go to Athena Console
- Set S3 output location (e.g., `s3://athena-query-results/`)
- Use SQL queries on `cleaned_tractor_data` to explore:
```sql
SELECT device_id, fuel_level, speed_kph, engine_temp
FROM tractor_telemetry_db.cleaned_tractor_data
WHERE fuel_level < 30
```

---

### 5. Amazon QuickSight

- Connect QuickSight to Glue Data Catalog or S3
- Import `cleaned_tractor_data` table
- Create dashboards:
  - Fuel level trends
  - GPS-based equipment heatmaps
  - High temperature alerts

---

### 6. Amazon SageMaker

- Use S3 cleaned data as input
- Build a Jupyter notebook to:
  - Train model (e.g., XGBoost or sklearn)
  - Predict failures or fuel depletion
- Deploy model to SageMaker endpoint for inference

---

## 🛡️ IAM Roles Required

- IoT → S3 write access
- Glue → S3 read/write, Catalog access
- Athena → S3 access for results
- QuickSight → Access to S3 or Athena
- SageMaker → Read data, write models to S3

---

## ✅ Outcomes

- Real-time data streaming and ingestion
- Automated ETL and schema discovery with Glue
- Interactive querying with Athena
- Visual dashboards in QuickSight
- Predictive insights using SageMaker

---

## 📂 Folder Structure

```
.
├── iot_simulator.py
├── certificates/
│   ├── AmazonRootCA1.pem
│   ├── device.cert.pem
│   └── device.private.key
├── glue_jobs/
├── athena_queries/
├── quicksight/
├── sagemaker/
└── README.md
```

---

## 📘 References

- [AWS IoT Core Docs](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html)
- [AWS Glue Docs](https://docs.aws.amazon.com/glue/)
- [Amazon Athena Docs](https://docs.aws.amazon.com/athena/)
- [Amazon QuickSight Docs](https://docs.aws.amazon.com/quicksight/)
- [Amazon SageMaker Docs](https://docs.aws.amazon.com/sagemaker/)
