# Workflow Orchestration with Kestra and BigQuery

## Overview

This project demonstrates the use of **Kestra** for workflow orchestration with **Google BigQuery** as a data warehouse.  
Below are the steps I followed to complete the assignment.

## Steps

### 1. Running Kestra Locally

I started by running Kestra locally using Docker:

```sh
docker run --pull=always --rm -it -p 8080:8080 --user=root \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /tmp:/tmp kestra/kestra:latest server local
```

### 2. Setting Up Google Cloud Platform (GCP)
- Created a service account in GCP
- Downloaded the service account key in JSON format

### 3. Configuring Environment Variables in Kestra
To allow Kestra to interact with GCP services, I created a flow that stores necessary environment variables:
- `GCP_CREDS`
- `GCP_PROJECT_ID`
- `GCP_LOCATION`
- `GCP_BUCKET_NAME`
- `GCP_DATASET`

[01_gcp_kv.yaml](https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/01_gcp_kv.yaml)

### 4. Creating a GCS Bucket and BigQuery Dataset
I used Kestra to create:
- Google Cloud Storage (GCS) bucket
- BigQuery dataset 

[02_gcp_setup.yaml](https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/02_gcp_setup.yaml)

### 5. Ingesting CSV Data into BigQuery
Created a flow that:
1. Downloads CSV files
2. Uploads them to the GCS bucket
3. Loads the data into BigQuery

[03_gcp_taxi.yaml](https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/03_gcp_taxi.yaml)

### 6. Scheduling
To automate the process, I modified the existing flow to include a schedule trigger for daily execution.

[03_gcp_taxi_scheduled](https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/03_gcp_taxi_scheduled)

### 7. Backfilling Historical Data
Configured the trigger with `backfill: true` and specified the date range (`startDate` to `endDate`) to upload historical data.  
Screenshots below illustrate the workflow configuration and execution.  

#### Green Taxi:  
<img src="https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/green_backfill_2021.png" alt="green_taxi" width="300" />  

#### Yellow Taxi:  
<img src="https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/flows/yellow_backfill_2021.png" alt="green_taxi" width="300" />

## Quiz Questions

1. Within the execution for Yellow Taxi data for the year 2020 and month 12: what is the uncompressed file size (i.e. the output file yellow_tripdata_2020-12.csv of the extract task)?
- `128.3 MB`
<img src="https://github.com/VMynenko/DE-Zoomcamp-Homework-2/blob/main/yellow_tripdata_2020-12.png" alt="yellow_tripdata_2020-12" width="300" />

2. What is the rendered value of the variable file when the inputs taxi is set to green, year is set to 2020, and month is set to 04 during execution?
- `green_tripdata_2020-04.csv`

3. How many rows are there for the Yellow Taxi data for all CSV files in the year 2020?
- 24,648,499
```sql
SELECT COUNT(1)
FROM `mindful-origin-424312-u0.de_zoomcamp_hw_2.yellow_tripdata` 
WHERE filename LIKE 'yellow_tripdata_2020%'
```
4. How many rows are there for the Green Taxi data for all CSV files in the year 2020?
- 1,734,051
```sql
SELECT COUNT(1) 
FROM `mindful-origin-424312-u0.de_zoomcamp_hw_2.green_tripdata` 
WHERE filename LIKE 'green_tripdata_2020%'
```

5. How many rows are there for the Yellow Taxi data for the March 2021 CSV file?
- 1,925,152
```sql
SELECT COUNT(1)
FROM `mindful-origin-424312-u0.de_zoomcamp_hw_2.yellow_tripdata` 
WHERE filename = 'yellow_tripdata_2021-03.csv'
```

6. How would you configure the timezone to New York in a Schedule trigger?
- Add a timezone property set to America/New_York in the Schedule trigger configuration
