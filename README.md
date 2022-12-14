# SQLOptimizationForApplications
Approaching SQL Optimizations in Application scenarios:

This document provides guidance at a high level to perform a health check to ensure the application and database is functioning as expected to avoid any issues which may lead to application Downtime. 

Step 1: Understand Customer SQL Landscape and applications. 

1. Understand SKU's. Compare vcore vs DTU model 
2. Review metrics of databases like CPU, memory, storage. 
3. Understand current database consumption and growth expected for short and longer term.
4. Understand database modeling and application query requirements.
5. Understand reporting and transactional needs for the database.
6. Understand networking and connectivity modes from Applications. 
7. Understand how the queries are submitted from application to database. 
8. Capture any errors (sql or networking). 

Performance optimizations:

Tailor the performance recommendations based on the issues identified in step 1. 

1. Review customer indexing policies.
2. Leverage Automatic tuning.
3. Review degree of parallelism
4. Review column store index.
5. Review data compression. 



Cost optimizations:

1. Review the current pricing model and do a comparison to check ideal sku for the workload.
2. Leverage AHUB and reservations. 
3.
4.
5.
6.

 Application Specific optimizations:
1.	Review sending queries as batch vs sequential executions: How to use batching to improve application performance - Azure SQL | Microsoft Docs
2.	Review networking, the connection policy was set to "Proxy". Recommendation is to use "Redirect Policy" for all connections originating inside Azure and Proxy for all connections from outside azure. Azure SQL Database connectivity architecture - Azure SQL Database and Azure Synapse Analytics | Microsoft Docs


Recommendations to Archive Data:
1.	The strategy to archive the data depends on factors such as volume, data requirement, data model dependencies. We can qualify the below data for data archival. 
•	Email and Audit data beyond some time frame.
•	Older versions of data which may no longer be needed.  
2.	Once we identify the data in step 1, we can have ways to have process to offload the data to storage (ADLS Gen2)using Azure data factory templates. Delta copy from a database using a control table - Azure Data Factory | Microsoft Docs
3.	Old data backed up to Azure storage can be purged in database using purging logic in stored procedures.
4.	Review backup strategy and we can perform point in time restores for any issues after purge and we want to restore the database back to original stage. 
5.	The files landed on the storage can be queried using Azure Synapse serverless pools as necessary. Please note that there is no pre-provisioned compute for serverless. The charge is only when queried based on the data processed.Serverless SQL pool - Azure Synapse Analytics | Microsoft Docs 

As another option,
We can offload data to Synapse Dedicated pool using Synapse link for SQL. Announcing the Public Preview of Azure Synapse Link for SQL - Microsoft Tech Community Although the cost of Synapse dedicated pool might be higher, it offers a centralized data warehousing capability which can be leveraged to offload data from multiple application databases and the data currently served for reports. Synapse dedicated pool can infinitely scale and offers performance over large dataset.  


Future Application Considerations( Not for current bids and tender Application, unless there are any new requirements):
 
The Application storage option is mainly driven by application specific requirements. We can consider these solutions when there is large database needs, high throughput, low latency and the data storage requirements are complex. 
1.	Azure Cosmos DB :  There are applications which can have fluid schema requirements, store complex relationships, hierarchical data. NoSQL offers such flexibility, the link provides some of the key benefits: Understanding the differences between Azure Cosmos DB NoSQL and relational databases | Microsoft Docs
2.	Azure Cache for Redis : This works well when there is read heavy workloads as Redis provides in-memory cache for high performance and scalable architectures. What is Azure Cache for Redis? | Microsoft Docs
3.	Azure SQL Hyperscale : Part of SQL family which provides HTAP capabilities to provide best of transactional and analytics together. Offers more compute and storage than Azure SQL databases. What is the Hyperscale service tier? - Azure SQL Database | Microsoft Docs
4.	Azure SQL Read replicas: To shift any kind of read workloads away from the transactional databases. This offering maintains a replica instance with near real time data. Read queries on replicas - Azure SQL Database | Microsoft Docs
