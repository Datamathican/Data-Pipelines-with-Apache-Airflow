# 🎵 Sparkify Data Pipeline with Apache Airflow

![Python](https://img.shields.io/badge/Python-3.8-blue.svg) ![Apache Airflow](https://img.shields.io/badge/Apache%20Airflow-2.0+-orange.svg) ![AWS](https://img.shields.io/badge/AWS-Redshift%20%7C%20S3-232F3E.svg) ![ETL](https://img.shields.io/badge/ETL-Pipeline-green.svg)

## 📖 Project Overview
This project automates and monitors a data warehouse ETL pipeline for a music streaming startup, Sparkify, using **Apache Airflow**. The pipeline extracts JSON logs and song metadata from **AWS S3**, stages them in **Amazon Redshift**, and transforms them into a star schema for analytical purposes.

## 🏗️ Architecture & Data Flow
The pipeline follows a specific dependency flow to ensure data integrity:
1.  **Start:** `Begin_execution` dummy task.
2.  **Staging:** `Stage_events` and `Stage_songs` run in parallel to load S3 data into Redshift.
3.  **Fact Loading:** `Load_songplays_fact_table` processes staged data.
4.  **Dimension Loading:** Four tasks (`Load_user_dim_table`, etc.) run in parallel to populate the star schema.
5.  **Quality Control:** `Run_data_quality_checks` validates the final datasets.
6.  **End:** `Stop_execution` task.

## ✨ Key Features & Technical Highlights
* **Custom Stage Operator:** Uses a `COPY` statement to load JSON files from S3, with support for templated fields to handle backfills based on execution time.
* **Flexible Dimension Loading:** Implements a "truncate-insert" pattern with a parameter to toggle between append-only and delete-load functionality.
* **Automated Quality Checks:** A parameterized operator that runs SQL test cases and compares results against expected values, raising exceptions on failure.
* **Robust Configuration:** DAG is configured with 3 retries, 5-minute delays, and no catchup for efficiency.

## 📂 Project Structure
```bash
Data-Pipelines-Airflow
├── dags
│   └── final_project.py        # Main DAG definition with task dependencies
├── plugins
│   ├── helpers
│   │   └── sql_queries.py      # Provided SQL transformations
│   └── operators
│       ├── stage_redshift.py   # S3 to Redshift staging logic
│       ├── load_fact.py        # Fact table loading logic
│       ├── load_dimension.py   # Dimension table loading logic
│       └── data_quality.py     # Data validation logic
├── create_tables.sql           # SQL DDL for Redshift schema
└── README.md

🚀 How to Run
Prerequisites: Create an IAM user and a Redshift Serverless workgroup in AWS.

S3 Setup: Copy the source data from s3://udacity-dend/ to your own S3 bucket using the AWS CLI.

Airflow Connections: Define aws_credentials and redshift connections in the Airflow UI.

Schema: Run the queries in create_tables.sql in your Redshift editor.

Trigger: Turn the DAG on in the Airflow Web Server (localhost:8080).
