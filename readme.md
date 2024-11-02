# Shellcorp Architecture

![image](https://github.com/user-attachments/assets/eb4f4eb9-3a6a-495a-ac33-611c373e68a7)

Assumptions:
 - No mature SRE function
 - Solo Data Engineer
 - No strong budget limits

Key points:
- Operational DB is suffering from performance issues due to high traffic: In order to alliviate that we need to move some of the traffic to an analytics DB.
- As most of the insight and analytics processes use data in the operational DB, we will need to replicate some/all of the tables to the new analitycs platform. To do that will leverage Change Data Capture CDC available in  SQL Server.
- Once CDC is enable and after the initial load, we will move the data from the CDC tables into the analytics platform incrementally using an ELT tool
- Within the analytics platform the data will be augmented, cleaned, aggregated and ready to be consumed by a reporting tool.

# Vendor Selection
ELT: Fivetran :There are several factors impacting the decision of the ingestion tool. I have choosen Fivetran as it has native and excellent SQL Server CDC connector that makes very easy to use. It is also a very scalable saas tool not requiring cloud infra. It would help delivering to prod quickly and let the solo DE focused in other areas like data modelling and DW set up.
See below some other alternatives depending on those factors:
- Real time analytics and multi consumer: In case that real time analytics is required probably debezium plus Kafka topics would be a better solution. Streaming brokers like Kafka allows multiple consumers and would be easy to share those CDC events with other systems if that is the requirement
- Infra: If the company has already a K8s cluster, we could use to deploy an open source orchestrator tool like Airflow. This tool comes with native SQL Server operators. This tool is python based and allows deliver every ETL process as code. This facilitates collaboration and peer reviewing. 
- There is also a tool called Astronomer that offers Airflow as a service that would help to have airflow as Saas. Which is also a very good solution but would need quite lots development to achieve a very robust CDC ingestion

DWH: Snowflake. Another fully Saas tool not like Databricks (control pane needs to be in the customer cloud) that would not require any infra set up. It includes capability for ML with snowpark and allows the use of snowflake notebooks for experimentation. It has good performance and support open source table format like iceberg. It could be expensive if not set up properly. There are other less expensive alternatives like Firebolt but they do not offer ML and notebooks capability.

Transformation tool DBT: DBT Core is an open source tool that can be plug to the ELT tool like Fivetran and allows to create SQL models. It is SQL native and can really help Analyst to create datasets in a centralised manner via git. It allows collaboration and cross-functional knowledge

Visualisation Tool: Looker. Looker allows defining metrics in a semantic layer. Metrics can be defined in a centralised repo and share them across the business.I would say that this semantic layer is the difference with other Visualisation tools like Tableau or power BI

# Other features
Once the data platform is mature it would be easy to use it to ingest new sources:
- Third party data to enrich our DWH
- API calls to monitor the health of our operational system
- Clickstream data like google analytics or adobe analytics to understand user journey

We could move some of the jobs out of the operational data.
- Files extract
- Reverse ETL processes
- Etc...

Obviously all the transactional jobs will run against the operational database (for example activities end-dated or status change) but we could leverage the ETL to scheduled them and have centralised place to run those jobs.

We could embed reports in the customer portal and show some analytics to the users without impacting operational databases
