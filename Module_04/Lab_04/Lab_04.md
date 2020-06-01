# Lab Four
## Clustering and Micro-Partitions in Snowflake
Earlier we talked about clustering and micro-partitions in SnowFlake and what they are.  
![Image of a clustered table with 4 micro-partitions](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/clustering.png)
The table consists of 24 rows stored across 4 micro-partitions, with the rows divided equally between each micro-partition. Within each micro-partition, the data is sorted and stored by column, which allows Snowflake to prune by:
1. micro-partition fisrt 
2. columns second 

### Let's create some tables
We will take data that Snowflake already has in the **Snowflake_sample_data**
First we need to take a look at what the data looks like
*it is very important to understand the data before you build a table*
Right click on the table **Customers** and choose *preview data*
This is what it will look like
![Image of data in the customer table](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/1_describe_table.png)
We see: 
- customerkey 
- name (which is just the custkey with customer in front of it) 
- address
- nation key
- phone number 
- account balance
- marketing segemnt 
- comments 

**Question** 
At first glance what might be a good column to cluster the data on?

![Image of creating the a table without clusters](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/2_create_nocluster_table.png)
This the SQL code 
```sql

CREATE OR REPLACE TRANSIENT TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER
AS
SELECT * FROM SNOWFLAKE_SAMPLE_DATA.TPCH_SF10000.CUSTOMER;

```
![Image of Clicking on QP](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/4_qp_create_nocluster_table.png)

![Image of Clicking on QP step1](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/5_qp_create_nocluster_table_step1.png)

![Image of Clicking on QP step2](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/6_qp_create_nocluster_table_step2_create.png)

![Image of Clicking on QP tablescan](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/7_qp_create_cluster_table_step2_scan.png)

One thing to take note is that I used a warehouse that was xsmall so it took over 30 mins to create the table 

**Question** What can I do to speed up the creation of the table? 

![Image of Clicking on QP 60% complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/8_qp_create_cluster_table_step2_create_40.png)

![Image of Clicking on QP 90% complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/9_qp_create_cluster_table_step2_create_70.png)

```sql

DESC TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER

```

```sql

SELECT SYSTEM$CLUSTERING_INFORMATION('MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER','(C_CUSTKEY)');
SELECT SYSTEM$CLUSTERING_INFORMATION('MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER','(C_MKTSEGMENT)');

```

![Image of clustering info_marketing](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/10_clustering_info_nocluster_market_imediate.png)

![Image of clustering info_customer key](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/11_clustering_info_nocluster_cust_key.png)

**Question** What is different about the two items that clustering info?  
 
 *Take note of comment for the clustering information on the customer key.  It let's you know that this might not be the best metric to create a cluster.*
 
 



