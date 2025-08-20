AWS Event-Driven ETL Pipeline

This project demonstrates the creation of a fully automated, event-driven ETL (Extract, Transform, Load) pipeline on Amazon Web Services (AWS). The pipeline processes JSON transactional data, transforms it into Parquet format, and makes it queryable in near real-time with Amazon Athena.

It’s designed for beginners and aspiring AWS Data Engineers, covering core AWS services like S3, Lambda, Glue, and Athena.

🚀 Project Overview

When a new JSON file is uploaded to S3, the pipeline automatically:

Reads and flattens the nested JSON.

Converts it into Parquet format.

Stores it in a data lake (S3).

Updates metadata with AWS Glue Crawler.

Makes the data instantly queryable in Athena.

For example, uploading a file with 23 orders directly reflected as 23 rows in Athena — all without manual steps.

🏗️ Architecture

Upload JSON → S3 (orders_json_incoming)

S3 event → Lambda Trigger

Lambda Function

Flattens nested JSON using Pandas

Converts to Parquet

Stores in orders_parquet_data_lake

Triggers AWS Glue Crawler

Glue Crawler updates Glue Data Catalog with schema

Athena queries data in near real-time

(Architecture diagram suggestion: S3 → Lambda → S3 (Data Lake) → Glue → Athena)

🛠️ AWS Services Used

Amazon S3 → Stores raw JSON and transformed Parquet data

AWS Lambda → Serverless compute for JSON flattening & Parquet conversion

AWS Glue Crawler → Infers schema and updates Glue Data Catalog

Amazon Athena → Runs SQL queries directly on Parquet files in S3

AWS IAM → Manages permissions for Lambda, Glue, and S3

📋 Prerequisites

AWS Account (most resources covered under Free Tier)

Basic Python knowledge (Lambda code uses Pandas, boto3)

(Optional) Jupyter Notebook to test flattening logic locally

🔧 Setup & Implementation
1. Create S3 Buckets/Folders

orders_json_incoming → Raw JSON files

orders_parquet_data_lake → Transformed Parquet data

2. Create AWS Lambda Function

Runtime: Python

Add pandas layer

Read JSON → Flatten → Convert to Parquet → Write back to S3

Trigger Glue Crawler programmatically

3. Configure S3 Event Trigger

Event: s3:ObjectCreated:*

Prefix: orders_json_incoming/

Suffix: .json

4. Set Up Glue Crawler

Points to orders_parquet_data_lake

Updates Glue Data Catalog

Target Database: etl_pipeline

5. Query with Amazon Athena

Run SQL queries such as:

SELECT * FROM etl_pipeline.orders_parquet_data_lake;

▶️ How to Run

Upload a JSON file (e.g., orders_ETL.json) into orders_json_incoming.

Pipeline automatically:

Creates a Parquet file in orders_parquet_data_lake.

Runs Glue Crawler to update schema.

Makes data queryable in Athena.

Open Athena → Run queries → View processed data.

💰 Cost

Covered under AWS Free Tier for the first year.

Even after, cost is minimal (~₹10 INR per run).

🔮 Next Steps / Improvements

Implement data partitioning in S3 (e.g., by date) for faster Athena queries

Add error handling and logging in Lambda

Use Glue ETL Jobs for heavier transformations

📂 Project Files

lambda_function.py → Lambda code (flatten JSON, convert to Parquet, trigger Glue Crawler)

orders_ETL.json → Sample JSON input file

README.md → Documentation (this file)

🌟 Key Learnings

How to build a serverless ETL pipeline on AWS

Automating schema updates with Glue Crawler

Querying S3 data directly using Athena

Using event-driven architecture for real-time processing
