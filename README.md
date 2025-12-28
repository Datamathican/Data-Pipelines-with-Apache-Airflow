🎵 Sparkify Data Pipeline with Apache Airflow

📖 Project Overview
This project demonstrates the construction of a production-grade data pipeline using Apache Airflow for a fictional music streaming company, Sparkify.

The goal was to automate and monitor the ETL (Extract, Transform, Load) process. The pipeline extracts JSON logs (user activity) and metadata (song details) from AWS S3, stages the data in Amazon Redshift, and transforms it into a set of dimensional tables for analytics.

🏗️ Architecture & Data Flow
The Airflow DAG performs the following sequence of tasks:

Stage Data: Extracts JSON data from S3 and loads it into staging tables in Redshift.

Load Fact Table: Transforms staging data to populate the songplays fact table.

Load Dimension Tables: Populates users, songs, artists, and time dimension tables.

Data Quality Checks: Runs automated SQL-based checks to ensure data integrity (e.g., non-null primary keys).

✨ Key Features & Technical Highlights
Custom Airflow Operators: instead of using default operators, I built custom plugins to abstract complex logic:

StageToRedshiftOperator: dynamically loads data from S3 to Redshift using templated fields to handle timestamped files.

LoadDimensionOperator: supports switching between "append-only" and "insert-delete" (truncate) modes to handle idempotent loads.

DataQualityOperator: accepts flexible SQL queries and expected results to validate data automatically after the pipeline runs.

AWS Integration: Utilizes AwsHook and PostgresHook to securely manage credentials and connections between Airflow and Redshift.

Error Handling: The DAG is configured with retries and retry delays to handle network blips or temporary service unavailability.

📂 Project Structure
Bash

Data-Pipelines-Airflow
├── dags
│   └── final_project.py        # The main DAG definition file
├── plugins
│   ├── helpers
│   │   └── sql_queries.py      # SQL transformations class
│   └── operators
│       ├── stage_redshift.py   # Custom Operator: Copy S3 -> Redshift
│       ├── load_fact.py        # Custom Operator: INSERT Fact table
│       ├── load_dimension.py   # Custom Operator: INSERT/TRUNCATE Dims
│       └── data_quality.py     # Custom Operator: Data validation
├── create_tables.sql           # DDL queries to create Redshift tables
└── README.md

🛠️ Prerequisites
AWS Account with an IAM User having S3FullAccess and RedshiftFullAccess.

Amazon Redshift Cluster (created in us-west-2 to match the S3 bucket region).

Apache Airflow running locally or via Docker.

🚀 How to Run
Clone the Repository:

Bash

git clone https://github.com/your-username/sparkify-airflow-pipeline.git
Create Tables: Execute the SQL statements in create_tables.sql in your Redshift Query Editor to initialize the schema.

Configure Airflow Connections: Go to the Airflow UI (Admin > Connections) and add:

aws_credentials: Login (Access Key), Password (Secret Key).

redshift: Host (Redshift Endpoint), Schema (dev), Login (user), Password (pass).

Deploy: Copy the dags and plugins folders to your Airflow home directory.

Execute: Toggle the DAG to On in the Airflow UI.

📊 Data Schema
The project models data into a Star Schema optimized for queries on song play analysis:

Fact Table: songplays (records in log data associated with song plays).

Dimension Tables: users, songs, artists, time.
