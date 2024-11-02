# Shellcorp Architecture

Assumptions:
 - No mature SRE function
 - Solo Data Engineer
 - No strong budget limits

Key points:
- Operational DB is suffering from perfomance issues due to high traffic: In order to alliviate that we need to move some of the traffic to an analytics DB.
- As most of the insight and analytics processes used data in the operational DB, we will need to replicate some/all of the tables to the new analitics platform. To do that will leverage Change Data Capture CDC available in  SQL Server.
- Once CDC is enable and after the initial load, we will move the data from the CDC tables into the analytics platform incrementally using an ELT tool
- Within the analytics platform the data will be augmented, cleane, aggregated and ready to be consumed by a reporting tool.

# Vendor Selection
ELT: Fivetran :There are several factors impacting the decision of the ingestion tool. I have choosed fivetran as it has native SQL Server CDC connector that makes very easy to use. It is also a very scalable saas tool not requiring cloud infra. It would help delivering to prod quickly and let the solo DE focused in other areas like data modelling and DW set up.
See below some other alternatives depending on those factors:
- Real time analytics and multi consumer: In case that we need real time analytics this would not be a good solution as data would come in batches. If that is required probably debezium plus Kafka topics would be a better solution. It also would help some of the CDC events need to be tranferred to a another App or DB. They could consume directly from the kafka topics
- Infra: If the company has already a K8s cluster that we could use to deploy an open source orchestrator tool like airflow. This tool comes with SQL Server operators. This tool is python based and allows deliver every ETL process as code. This facilitates collaboration and peer reviewing. In my opinion this would be better when several DE in the team
- There is also a tool called Astronomer that offers Airflow as a service that would help to have airflow as Saas.

DWH: Snowflake
