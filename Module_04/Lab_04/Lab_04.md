# Lab Four
## Clustering and Micro-Partitions in SnowFlake
Earlier we talked about clustering and micro-partitions in SnowFlake and what they are.  
![Image of a clustered table with 4 micro-partitions]((./Images/clustering.png))
The table consists of 24 rows stored across 4 micro-partitions, with the rows divided equally between each micro-partition. Within each micro-partition, the data is sorted and stored by column






```sql

CREATE OR REPLACE TRANSIENT TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER
AS
SELECT * FROM SNOWFLAKE_SAMPLE_DATA.TPCH_SF10000.CUSTOMER;

```
