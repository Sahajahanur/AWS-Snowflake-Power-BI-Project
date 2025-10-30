# AWS-Snowflake-Power-BI-Project
This project demonstrates an **end-to-end cloud data pipeline** using **AWS S3**, **Snowflake**, and **Power BI**.   It showcases how raw data stored in Amazon S3 can be ingested into Snowflake, transformed with SQL logic, and visualized through Power BI dashboards.

## 🧭 End-to-End Project Flow

**1️⃣ AWS Layer**
- Create S3 bucket → `powerbi.project.srl`
- Upload dataset files to the S3 bucket
- Create IAM Role → `powerbi.role`

**2️⃣ Snowflake Layer**
- Create Storage Integration → `PBI_Integration`
- Create Database → `PowerBI`
- Create Schema → `PBI_Data`
- Create Table → `PBI_Dataset`
- Load data from S3 into Snowflake
- Transform data (Agriculture table, Year_Group, Rainfall_Group)

**3️⃣ Power BI Layer**
- Connect Power BI to Snowflake
- Build Rainfall Analysis page
- Add other analytical pages
- Publish final dashboard to Power BI Service

---

### 🔁 Simplified Flow Diagram

AWS S3 → Snowflake → Power BI  

✅ **Summary**
This project builds a complete cloud data pipeline:  
- **AWS S3** for data storage  
- **Snowflake** for integration, transformation, and data modeling  
- **Power BI** for visualization and reporting
  
 ## ⚙️ Project Setup & Execution
 ### **Step 1: Create Amazon S3 Bucket**
  - **Bucket Name:** `powerbi.project.srl`  
- Upload your dataset into this S3 bucket.

## Step 2: Create AWS IAM Role

Role Name: powerbi.role

Grant S3 read access and attach a trust relationship with Snowflake.

## Step 3: Create Snowflake Storage Integration

     CREATE OR REPLACE STORAGE INTEGRATION PBI_Integration
    TYPE = EXTERNAL_STAGE
    STORAGE_PROVIDER = 'S3'
    ENABLED = TRUE
    STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::048832330477:role/powerbi.role'
    STORAGE_ALLOWED_LOCATIONS = ('s3://powerbi.project.srl/')
    COMMENT = 'Storage integration between Snowflake and AWS S3'; 
### Verify integration:
    DESC INTEGRATION PBI_Integration;
### Step 4: Load Data into Snowflake    
    -- Create database and schema
    CREATE DATABASE PowerBI;
    CREATE SCHEMA PBI_Data;

    -- Create dataset table
    CREATE TABLE PBI_Dataset (
    Year INT,
    Location STRING,
    Area INT,
    Rainfall FLOAT,
    Temperature FLOAT,
    Soil_type STRING,
    Irrigation STRING,
    Yields INT,
    Humidity FLOAT,
    Crops STRING,
    Price INT,
    Season STRING
    );

    -- Validate data
    SELECT * FROM PBI_Dataset;

<img width="1207" height="571" alt="image" src="https://github.com/user-attachments/assets/067fbc97-542a-4cce-9ec8-1ef17c2e4b65" />

### Step 5: Understand the Data

Analyze dataset characteristics, identify nulls, outliers, and correlations before transformation.

<img width="1205" height="612" alt="image" src="https://github.com/user-attachments/assets/57de422f-e9fa-4b0a-a9ee-9af3fd710717" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Step 6: Transform Data in Snowflake SQL

    -- Create working copy
    CREATE TABLE Agriculture AS
    SELECT * FROM PBI_Dataset;

    -- Apply adjustments
    UPDATE Agriculture SET Rainfall = 1.1 * Rainfall;
    UPDATE Agriculture SET Area = 0.9 * Area;

    -- Add and populate Year_Group column
    ALTER TABLE Agriculture ADD Year_Group STRING;

    UPDATE Agriculture SET Year_Group = 'Y1' WHERE Year BETWEEN 2004 AND 2009;
    UPDATE Agriculture SET Year_Group = 'Y2' WHERE Year BETWEEN 2010 AND 2015;
    UPDATE Agriculture SET Year_Group = 'Y3' WHERE Year BETWEEN 2016 AND 2019;

    SELECT * FROM Agriculture;
    
### Step 7: Add Rainfall Group Column
    -- Define rainfall categories
    -- Range: Min = 255 | Max = 4103

    ALTER TABLE Agriculture ADD Rainfall_Groups STRING;

    UPDATE Agriculture SET Rainfall_Groups = 'Low'
    WHERE Rainfall >= 255 AND Rainfall < 1200;

    UPDATE Agriculture SET Rainfall_Groups = 'Medium'
    WHERE Rainfall >= 1200 AND Rainfall < 2800;

    UPDATE Agriculture SET Rainfall_Groups = 'High'
    WHERE Rainfall >= 2800;

    SELECT * FROM Agriculture;

### Step 8: Import Data into Power BI

* In Power BI Desktop:

* Click Get Data → Snowflake

* Provide:

* Server: <your_account>.snowflakecomputing.com

* Warehouse: <your_warehouse>

* Database: PowerBI

* Schema: PBI_Data

* Choose DirectQuery for live data or Import for scheduled refresh

* Load the Agriculture table

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 9: Build Power BI Rainfall Analysis Page
* Create visuals to show:

* Average rainfall by year group

* Rainfall group distribution by crop and season

* Correlation between rainfall and yield

<img width="1897" height="912" alt="image" src="https://github.com/user-attachments/assets/e83eb28b-8203-4777-a8a6-b2d37825b023" />

------------------------------------------------------------------------------------------------------------------------------------

### Step 10: Add Additional Pages

Enhance insights with extra report pages:

🌡️ Temperature Impact Analysis

    
<img width="1863" height="945" alt="image" src="https://github.com/user-attachments/assets/5b1341ed-4396-4ac7-9451-d9b55d2d7faf" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

🌦️ Yield Analysis

<img width="1867" height="897" alt="image" src="https://github.com/user-attachments/assets/87601409-6940-4022-b955-fa75c4a1012c" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------

🌾 Humidity Analysis

<img width="1677" height="941" alt="image" src="https://github.com/user-attachments/assets/fecd6e76-f8aa-4272-b669-a4888b65e09e" />

------------------------------------------------------------------------------------------------------------------------------------------------------------

## Step 11: Publish to Power BI Service

* Sign in to Power BI Service

* Publish the report from Power BI Desktop

* Schedule refresh (if using Import mode)

* Share dashboards with stakeholders

Link : https://app.powerbi.com/groups/me/reports/4a2ba6df-171c-43c1-aa82-b0104e4d6454/513a946c318dee6a976c?experience=power-bi

## 🧰 Tools & Technologies

| Tool          | Purpose                                      |
| ------------- | -------------------------------------------- |
| **AWS S3**    | Data storage (raw & processed)               |
| **Snowflake** | Data warehousing and transformation          |
| **Power BI**  | Visualization & analytics                    |
| **SQL**       | Transformation logic                         |
| **IAM Role**  | Secure integration between AWS and Snowflake |


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 🔧 Configuration Summary

| Parameter       | Value                       |
| --------------- | --------------------------- |
| **S3 Bucket**   | `s3://powerbi.project.srl/` |
| **IAM Role**    | `powerbi.role`              |
| **Integration** | `PBI_Integration`           |
| **Database**    | `PowerBI`                   |
| **Schema**      | `PBI_Data`                  |
| **Table**       | `Agriculture`               |

## 🧩 Future Enhancements

* Automate data ingestion using Snowpipe

* Implement CI/CD with GitHub Actions or Terraform

* Add role-based access control in Snowflake

* Integrate monitoring using AWS CloudWatch + SNS



  
         


                                                                                                                                                                       

 






