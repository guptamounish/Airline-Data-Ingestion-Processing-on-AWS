# Airline Data Ingestion & Processing on AWS

## Project Overview

The Airline Data Ingestion & Processing Project is a cloud-based ETL pipeline built on AWS to process airline flight data. The project automates data ingestion, transformation, and storage using AWS S3, Glue, Redshift, EventBridge, Step Functions, and SNS. The processed data is stored in Amazon Redshift for further querying and analysis.


**BUSINESS LOGIC**

The business logic of the Airline Data Ingestion & Processing Project on AWS centers around automating the extraction, transformation, and loading (ETL) of airline flight data, leveraging various AWS services to create a scalable and efficient cloud-based pipeline. Here's a breakdown of the key components and their roles in the business logic:

**Data Ingestion:**

**AWS S3:** Acts as the initial landing zone for flight data in CSV format. This is where raw data files are uploaded by airline clients.
**AWS EventBridge:** Monitors the S3 bucket for new data uploads and triggers the ETL process via Step Functions. This automation ensures that data processing begins as soon as new data arrives.
**Data Processing (ETL):**

**AWS Glue Crawler:** Automatically scans and catalogs data in S3, preparing it for transformation by identifying formats and schemas, which facilitates metadata management.
**AWS Glue Jobs:** The core of the ETL process, where raw flight data is transformed. This involves reading the data from S3, enriching it with additional details like airport codes, and then preparing it for analysis by transforming and loading it into Redshift.
**Data Storage and Querying:**

**Amazon Redshift:** Serves as the analytical database where processed data is stored. It provides the capability to run complex queries over processed data, enabling insights and analytics on flight patterns, delays, etc.
**Schema Management:** The creation of specific schemas and tables in Redshift to store processed data in an organized manner, optimizing for query performance.
**Orchestration and Monitoring:**

**AWS Step Functions:** Orchestrates the sequence of tasks from data ingestion to storage, including the execution of Glue jobs and crawlers. This manages task dependencies and handles error checking, ensuring the ETL process is robust.
Amazon SNS: Used for sending notifications on the status of the Glue jobs, which helps in monitoring the health of the ETL process and quickly addressing any issues.
**Operational Efficiency:**

**Permissions and Policies:** Proper AWS IAM roles and policies are crucial for securing and managing access to AWS resources, ensuring that each component operates within its defined scope.
**Automation:** The use of EventBridge and Step Functions automates the workflow, reducing manual overhead and speeding up the process from data upload to insights generation.
By automating the ingestion, transformation, and loading of flight data, the project enables timely and efficient data processing on a scalable cloud platform. This automation not only minimizes the time to insight for airline data analytics but also reduces the possibility of human error and operational costs, making it a vital tool for any data-driven decision-making process in the aviation sector.

## Project Architecture

![Project Architecture](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20Architecture.png)

The architecture consists of:

1.	Data Source: Flight data (airport_dim and flight_raw) in CSV format is uploaded to an S3 bucket from airline client and flight_raw is copied to redshift.

2.	Glue Crawler: Crawl the the airport_dim data and processed flight_raw data from redshift and store the data in glue data catalog.
 
3.	Event Bridge Rule: AWS EventBridge detects new file uploads and triggers a Step Function workflow.
 
4.	Step Functions: Automates the ETL process by orchestrating Glue Crawler and Glue Jobs.
 
5.	AWS Glue ETL: Processes and transforms raw flight data.
 
6.	Amazon Redshift: Stores the cleaned and processed flight data for querying.
 
7.	Amazon SNS: Sends notifications about job status.

## Project Execution on AWS

1. Setting Up EventBridge

![Event_Bridge_Setup.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Event_Bridge_Setup.png)

EventBridge monitors the S3 bucket for new CSV files and triggers Step Functions.

EventBridge Rule Configuration (event_bridge_rule.json):

```
{
  "source": ["aws.s3"],
  "detail-type": ["Object Created"],
  "detail": {
    "bucket": {
      "name": ["airlines-data-ingestion-project"]
    },
    "object": {
      "key": [{
        "suffix": ".csv"
      }]
    }
  }
}
```
![Event_Bridge_Permision_Policies.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Event_Bridge_Permision_Policies.png)

2. Setting Up AWS Glue ETL Job

The Glue job (glue_etl_job.py) extracts flight data from S3, enriches it with airport details, and loads it into Redshift.

![Glue_ETL_Job_Status.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Glue_ETL_Job_Status.png)

Key steps:
	
 •	Read raw flight data from S3.
	
 •	Join with airport codes for enrichment.
	
 •	Store processed data in Amazon Redshift.

3. Step Functions Orchestration

Step Functions automate the execution of Glue Crawlers and Glue Jobs.

![Step_Function_Job_Graph_View.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Step_Function_Job_Graph_View.png)

Configuration (step_function_config.json):
	
 •	Starts Glue Crawlers to catalog raw data.
	
 •	Checks for crawler completion.
	
 •	Runs Glue ETL job to transform data.
	
 •	Sends success/failure notifications via SNS.


![Step_Function_Job_Setup_Graph_View.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Step_Function_Job_Setup_Graph_View.png)

![Step_Function_Permision_Policies.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Step_Function_Permision_Policies.png)

![Step_Function_Status.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Step_Function_Status.png)

4. Amazon Redshift for Processed Data Storage

Redshift tables store the cleaned flight data for analysis.

Schema & Table Creation (redshift_create_table_commands.txt):

```
CREATE TABLE airlines.daily_flights_processed (
    carrier VARCHAR(10),
    dep_airport VARCHAR(200),
    arr_airport VARCHAR(200),
    dep_city VARCHAR(100),
    arr_city VARCHAR(100),
    dep_state VARCHAR(100),
    arr_state VARCHAR(100),
    dep_delay BIGINT,
    arr_delay BIGINT
);
```
![Redshift_Cluster_Setup.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Redshift_Cluster_Setup.png)

![Redshift_Permision_Policies.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Redshift_Permision_Policies.png)

Querying processed data:

```
SELECT * FROM airlines.daily_flights_processed LIMIT 5;

```
![Querying_Flight_Processed_Data_in_Redshift.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/Querying_Flight_Processed_Data_in_Redshift.png)

5. S3 Data Storage

Flight data and airport codes are stored in S3 before processing.

![S3_Setup_for_Airportdim_and_Flight_Data.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/S3_Setup_for_Airportdim_and_Flight_Data.png)

6. SNS Notifications for Job Status

Amazon SNS sends notifications on Glue job success/failure.
![SNS_Notification_of_Job_Status.png](https://github.com/guptamounish/Airline-Data-Ingestion-Processing-on-AWS/blob/main/Project%20execution%20screenshot%20on%20AWS/SNS_Notification_of_Job_Status.png)


How to Run the Project
	
 1.	Upload new flight data to the S3 bucket (airlines-data-ingestion-project).
	
 2.	EventBridge triggers the Step Function workflow.
	
 3.	Glue ETL job processes the data.
	
 4.	Processed data is stored in Amazon Redshift.
	
 5.	Query Redshift for flight insights.

## Conclusion

This project automates airline data ingestion, transformation, and storage using AWS services, making it scalable and efficient. 🚀

