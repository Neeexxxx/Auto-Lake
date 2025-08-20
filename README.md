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

<p align="center">
<img width="803" height="176" alt="Screenshot 2025-08-20 131757" src="https://github.com/user-attachments/assets/56fbf3d4-935f-49cc-bd01-81527d90f6d8" />
</p>



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
- S3 Bucket Name → `namaste_etl_project`
- Create 2 folder inside bucket
- `orders_json_incoming` → Raw JSON files
- `orders_parquet_data_lake` → Transformed Parquet data
  <p align="center">
  <img width="1917" height="797" alt="image" src="https://github.com/user-attachments/assets/9fb36bca-4d0c-4c4d-9b8c-a291e4872cac" />
  </p>


### 2. Create AWS Lambda Function  
- Create Lambda Function → `my_etl_pipeline`
  <p align="center">
  <img width="1905" height="812" alt="image" src="https://github.com/user-attachments/assets/a15f6c9a-cd42-4427-b844-1543e64540e3" />
  </p>

- Now, we want to read data from S3 on this function
- Go to Configuration → permissions → Click on Role name
- Now add policy to read data from S3
- Go to Add permissions → Attach policy
- Search for AmazonS3FullAccess select it and Click on add permissions
  
<p align="center">
  <img width="1911" height="812" alt="image" src="https://github.com/user-attachments/assets/ca292405-46ec-4cb1-b40c-cd161970489a" />
</p>

- Go to lambda function → Add trigger
- Select S3 as trigger Configuration
- Choose the bucket name that you have already created
- Put `orders_json_incoming/` in prefix
- for Suffix `json`
  
<p align="center">
  <img width="1918" height="812" alt="image" src="https://github.com/user-attachments/assets/20f8d414-5dee-4795-95e9-59754fd8ad86" />
</p>

- Click Add and the trigger is added
- Go to Code
- Paste the code that is given in lambda_function.py
- After pasting the code Click on Deploy(Ctrl+Shift+U)
- Below there will be option of add layer
- Select AWSSDKPandas-Python313
- Click Add
  
<p align="center">
  <img width="1918" height="766" alt="image" src="https://github.com/user-attachments/assets/30651af2-81b7-4aef-83fb-adf81eadb4ac" />
</p>
   
- To check wheather trigger is working or not
- Go to `orders_json_incoming` folder in S3
- Upload `orders_ETL.json`
- Go to lambda → Monitor → View CloudWatch logs( When ever the Lambda is triggered logs will be created in CloudWatch logs)
- When we will go to `orders_parquet_datalake` in S3, you will get parquet file create i.e tabular representation


### 3. Set Up Data Catalog using AWS Glue
- Go to AWS Glue → Data Catalog → Databases
- Create a database i.e `etl_pipeline`
- Go to Crawlers → step 1 Create crawler i.e `etl_pipeline_crawler`
  
<p align="center">
  <img width="1918" height="805" alt="image" src="https://github.com/user-attachments/assets/c1cc06df-1089-4c03-bd62-ce04992948e5" />
</p>

- Step 2 Inside Data Source Configuration → Add a data source
- Browse S3, select s3 bucket (`namaste_etl_project`) and folder (`orders_parquet_datalake`)
  
<p align="center">
  <img width="1918" height="802" alt="image" src="https://github.com/user-attachments/assets/672228b3-aa46-49ef-b270-238848727b3f" />
</p>

- Step 3 Inside IAM Role → Create new IAM Role i.e `AWSGlueServiceRole-etl_pipeline_role`

<p align="center">
  <img width="1917" height="805" alt="image" src="https://github.com/user-attachments/assets/1e88218f-a74e-4b98-8e0e-ca96ea7c10b5" />
</p>

- Step 4 Set Output and scheduling → in target database select `etl_pipline`

<p align="center">
  <img width="1918" height="805" alt="image" src="https://github.com/user-attachments/assets/dba5fd32-7a7c-49bb-9502-382aab92aa5a" />
</p>

- Step 5 Review and Create
- Crawler will be created,once it is run it will create new table
- Go to Tables, where we can see the table `orders_parquet_datalake`
- We wil get Schema, to get table we need to go to Amazon Athena
  
<p align="center">
  <img width="1917" height="821" alt="image" src="https://github.com/user-attachments/assets/0fea915b-4174-4d1b-8d72-d319b96ec2e1" />
</p>


### 4. Query with Amazon Athena  
- Go to Amazon Athena
- Now, can perform various query

<p align="center">
  <img width="1918" height="822" alt="image" src="https://github.com/user-attachments/assets/f19de32d-1c51-4a7d-a9ce-e728eb621153" />
</p>

Run SQL queries such as:  
```sql
SELECT * FROM etl_pipeline.orders_parquet_data_lake;
```

# Output
<p align="center">
 <img width="1838" height="856" alt="image" src="https://github.com/user-attachments/assets/3ab9edf0-26e8-4bff-b83c-44905f26093a" />
</p>
<p align="center">
 <img width="1840" height="863" alt="image" src="https://github.com/user-attachments/assets/c9829030-8749-449c-9217-1a514dcffe4c" />
</p>
<p align="center">
   <img width="1905" height="817" alt="image" src="https://github.com/user-attachments/assets/1546b20e-38d8-4f88-b2df-d0b9724e4f92" />
</p>

## Cost 💰
- This project utilizes AWS Free Tier eligible services, ensuring low-cost deployment during development.
- **Important:** Charges may occur if the Free Tier limits are exceeded. It’s recommended to monitor your usage and shut down resources when not in use.

## 🤝 Contributions
You are welcomed! for any contributions, or suggestions. Any useful updation of code, or modification in a file is expected as a piece of contribution.

**Steps for contributions:**
1. ⭐ Star this repository.
2. Fork and Clone
3. Make an appropriate PR, and describing your changes in a systematic format.

## Acknowledgements 🙌
Thanks to AWS for providing robust cloud services for hosting and deployment.
Inspired by the love for simple, engaging memory games.

   
