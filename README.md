Web Server Log Analysis with Apache Hive

Project Overview
This project implements a Hive-based analytical process to analyze web server logs stored in CSV format. The primary goal is to extract meaningful insights regarding website traffic, user behaviors, and potential anomalies using Apache Hive.

Implementation Approach

1. Setup Hive Environment
   
Created a Hive database web_log_analysis.

Defined an external table web_logs to store web server logs.

3. Data Loading
 
Loaded CSV data into HDFS.

Imported the data into the web_logs Hive table.

4. Analysis Queries
   
Total Web Requests: Counted total entries in the logs.

Status Code Analysis: Calculated frequency of HTTP status codes (200, 404, 500).

Most Visited Pages: Identified the top 3 most visited URLs.

Traffic Source Analysis: Determined the most common user agents.

Suspicious Activity Detection: Found IP addresses with more than 3 failed requests (status 404 or 500).

Traffic Trends: Counted requests per minute to observe traffic patterns.

5. Partitioning for Optimization
   
Created a partitioned table by status code to optimize query performance.

Execution Steps

Start Hadoop and Hive services
docker compose cp -d

Create Hive database and tables:

CREATE DATABASE web_log_analysis;

USE web_log_analysis;

Load Data into Hive Table:

LOAD DATA INPATH '/user/hive/warehouse/web_logs/web_logs.csv' INTO TABLE web_logs;

Runned Analysis Queries

Execute the provided HiveQL script to perform all analysis tasks.

Implement Partitioning:

CREATE EXTERNAL TABLE IF NOT EXISTS web_logs_partitioned (

    ip STRING,
    
    timestamp STRING,
    
    url STRING,
    
    user_agent STRING
)
PARTITIONED BY (status INT)

ROW FORMAT DELIMITED

FIELDS TERMINATED BY ','

STORED AS TEXTFILE;

INSERT OVERWRITE TABLE web_logs_partitioned PARTITION (status)

SELECT ip, timestamp, url, user_agent, status FROM web_logs;

Challenges Faced

Data Format Issues: Resolved data parsing errors by adjusting field delimiters.

Sample Input and Output: 

Sample Input (CSV)

ip,timestamp,url,status,user_agent

192.168.1.1,2024-02-01 10:15:00,/home,200,Mozilla/5.0

192.168.1.2,2024-02-01 10:16:00,/products,200,Chrome/90.0

192.168.1.3,2024-02-01 10:17:00,/checkout,404,Safari/13.1

192.168.1.10,2024-02-01 10:18:00,/home,500,Mozilla/5.0

192.168.1.15,2024-02-01 10:19:00,/products,404,Chrome/90.0

Sample Output:

Total Requests: 101

Status Code Analysis:

200: 2

404: 2

500: 1

Top 3 Visited Pages:

/home: 2

/products: 2

/checkout: 1

Traffic Source Analysis:
Mozilla/5.0: 2

Chrome/90.0: 2

Safari/13.1: 1

Suspicious IP Addresses:

192.168.1.10: 1 failed request

192.168.1.15: 1 failed reque

