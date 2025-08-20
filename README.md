# Auto Lake - AWS Event-Driven ETL Pipeline  

This project demonstrates a **fully automated, event-driven ETL (Extract, Transform, Load) pipeline** on Amazon Web Services (AWS). The pipeline processes **JSON transactional data**, transforms it into **Parquet format**, and makes it **queryable in near real-time** with Amazon Athena.  

It’s designed for beginners and aspiring **AWS Data Engineers**, covering core AWS services like S3, Lambda, Glue, and Athena.  

---

## 🚀 Project Overview  

When a new JSON file is uploaded to S3, the pipeline automatically:  
1. Reads and flattens the nested JSON.  
2. Converts it into Parquet format.  
3. Stores it in a data lake (S3).  
4. Updates metadata with AWS Glue Crawler.  
5. Makes the data instantly queryable in Athena.  

For example, uploading a file with 23 orders directly reflected as 23 rows in Athena — all without manual steps.  

---

## 🏗️ Architecture  

1. **Upload JSON → S3** (`orders_json_incoming`)  
2. **S3 event → Lambda Trigger**  
3. **Lambda Function**  
   - Flattens nested JSON using Pandas  
   - Converts to Parquet  
   - Stores in `orders_parquet_data_lake`  
   - Triggers AWS Glue Crawler  
4. **Glue Crawler** updates **Glue Data Catalog** with schema  
5. **Athena** queries data in near real-time  

*(Architecture diagram suggestion: S3 → Lambda → S3 (Data Lake) → Glue → Athena)*  
<centre>
<img width="803" height="176" alt="Screenshot 2025-08-20 131757" src="https://github.com/user-attachments/assets/56fbf3d4-935f-49cc-bd01-81527d90f6d8" />
</centre>


---

## 🛠️ AWS Services Used  

- **Amazon S3** → Stores raw JSON and transformed Parquet data  
- **AWS Lambda** → Serverless compute for JSON flattening & Parquet conversion  
- **AWS Glue Crawler** → Infers schema and updates Glue Data Catalog  
- **Amazon Athena** → Runs SQL queries directly on Parquet files in S3  
- **AWS IAM** → Manages permissions for Lambda, Glue, and S3  

---

## 📋 Prerequisites  

- AWS Account (most resources covered under **Free Tier**)  
- Basic **Python** knowledge (Lambda code uses Pandas, boto3)  
- (Optional) **Jupyter Notebook** to test flattening logic locally  

---

## 🔧 Setup & Implementation  

### 1. Create S3 Buckets/Folders  
- `orders_json_incoming` → Raw JSON files  
- `orders_parquet_data_lake` → Transformed Parquet data  

### 2. Create AWS Lambda Function  
- Runtime: Python  
- Add **pandas layer**  
- Read JSON → Flatten → Convert to Parquet → Write back to S3  
- Trigger **Glue Crawler** programmatically  

### 3. Configure S3 Event Trigger  
- Event: `s3:ObjectCreated:*`  
- Prefix: `orders_json_incoming/`  
- Suffix: `.json`  

### 4. Set Up Glue Crawler  
- Points to `orders_parquet_data_lake`  
- Updates **Glue Data Catalog**  
- Target Database: `etl_pipeline`  

### 5. Query with Amazon Athena  
Run SQL queries such as:  
```sql
SELECT * FROM etl_pipeline.orders_parquet_data_lake;
