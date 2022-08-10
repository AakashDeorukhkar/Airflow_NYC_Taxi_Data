# Ingesting NYC Yellow Taxi Data to GCP using Airflow on Docker

Hosted Apache Airflow on Docker to manage the workflow of a data engineering pipeline. The DAG created has 3 major tasks:

#### 1) Download Dataset Task: 
This task is responsible to download the dataset from the dataset URL and save it locally on the Docker Airflow image by performing a Bash operation. 

#### 2) Local to GCS Task: 
This task takes in 3 parameters:
   - bucket: This is the GCS Bucket name specified in the docker-compose.yaml file
   - object_name: This parameter sets the target path its file-name
   - local_file: This parameter sets the source path of the file downloaded in the earlier step and its file-name
In this task, we call a Python function to upload the locally stored dataset on GCP's GCS. We first initialize a storage client and associate it with the Bucket. Then we upload the data from a local path to this Bucket.

#### 3) BigQuery External Table Task:
We initialize the BigQuery external table resources like Project ID, DataSet ID(set in Terraform), Table ID along with External Data Configurations like Data source format and the Source URI's(Reference to the GCS bucket that we uploaded data to in the previous step)

#### DEPENDENCIES
#### Download Dataset Task >> Local to GCS Task >> BigQuery External Table Task

#### DAG Workflow Graph:
![Airflow Server DAG Workflow](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/DAG_Workflow.png)

#### DAG Tree Graph:
![Airflow Server DAG Tree](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/DAG_Tree.png)

#### DAG Run:
![Airflow Server DAG Success](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/DAG_run.png)

#### DEPENDENCIES

#### Download Dataset Task >> Local to GCS Task >> BigQuery External Table Task

#### BigQuery External Table Schema:
![BigQuery External Table Schema](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/BigQuery_externaltable.png)

#### SELECT statement on External Table:
![BigQuery External Table Sample](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/BigQuery_externaltable_sample.png)

#### Creating a Non-partioned table in BigQuery:
QUERY: 
```
CREATE OR REPLACE TABLE `skillful-octane-358220.trips_data_all.non_partioned` AS
SELECT * FROM `skillful-octane-358220.trips_data_all.external_table`;
```
![BigQuery Non-partioned Table](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/BigQuery_non_partionedtable.png)

#### Creating Partioned(based on pick-up date) table in BigQuery:
QUERY:
```
CREATE OR REPLACE TABLE `skillful-octane-358220.trips_data_all.trip_data_partioned`
PARTITION BY 
DATE(tpep_pickup_datetime) AS
SELECT * FROM `skillful-octane-358220.trips_data_all.external_table`;
```
![BigQuery Partioned Table](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/BigQuery_partionedtable.png)


#### Creating Partioned(based on pick-up date) & Clustered(based on VendorID) table in BigQuery:
QUERY:
```
CREATE OR REPLACE TABLE `skillful-octane-358220.trips_data_all.trip_data_partioned_clustered`
PARTITION BY 
DATE(tpep_pickup_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `skillful-octane-358220.trips_data_all.external_table`;
```
![BigQuery Partioned & Clustered Table](https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Images/BigQuery_partionedAndClusteredtable.png)

#### Contents:
Dataset: https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2022-01.parquet 

Docker-compose.yaml : https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/docker-compose.yaml

Dockerfile: https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/Dockerfile

requirements.txt : https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/blob/main/requirements.txt

DAG: https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/tree/main/dags

Screenshot/Images: https://github.com/AakashDeorukhkar/Airflow_NYC_Taxi_Data/tree/main/Images


