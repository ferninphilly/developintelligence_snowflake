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
 
 60% complete
![Image of Clicking on QP 60% complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/8_qp_create_cluster_table_step2_create_40.png)
 90% complete
![Image of Clicking on QP 90% complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/9_qp_create_cluster_table_step2_create_70.png)

```sql

DESC TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER

```

```sql

SELECT SYSTEM$CLUSTERING_INFORMATION('MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER','(C_CUSTKEY)');
SELECT SYSTEM$CLUSTERING_INFORMATION('MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER','(C_MKTSEGMENT)');

```

![Image of clustering info_marketing](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/10_clustering_info_nocluster_market_imediate.png)

Remember this image that shows depth of micro-partitions

![Image of clustering info_marketing](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/overlapping.png)

![Image of clustering info_customer key](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/11_clustering_info_nocluster_cust_key.png)

**Question** What is different about the two items in the clustering info?  
 
 *Take note of comment for the clustering information on the customer key.  It let's you know that this might not be the best metric to create a cluster.*
 
### Creating the a table with clustering 

Let's create a table with clustering from the start. We will also switch to a Large warehouse to speed up the creation.  In order to see what how the table was created we can use the function **get_ddl**.

![Image of get_DDL](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Images/Lab_04/Images/get_ddl.png)

```sql

SELECT GET_DDL('table', 'CUSTOMER') 

```
This function allows you to see what the column names are and what the column types are.
 
Now we can create the new table that will be clustered.

![Image of get_DDL](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/12_create_cluster_table.png)

```sql

CREATE OR REPLACE TRANSIENT TABLE CUSTOMER_CLUSTER (
    C_CUSTKEY NUMBER(38,0) NOT NULL,
    C_NAME VARCHAR(25) NOT NULL,
    C_ADDRESS VARCHAR(40) NOT NULL,
    C_NATIONKEY NUMBER(38,0) NOT NULL,
    C_PHONE VARCHAR(15) NOT NULL,
    C_ACCTBAL NUMBER(12,2) NOT NULL,
    C_MKTSEGMENT VARCHAR(10),
    C_COMMENT VARCHAR(117)
 ) CLUSTER BY (C_MKTSEGMENT);
 
INSERT INTO MY_PRACTICE_DB.PRACTICE.CUSTOMER_CLUSTER
SELECT * FROM SNOWFLAKE_SAMPLE_DATA.TPCH_SF10000.CUSTOMER;

```

This table was created much faster with the large data warehouse.
*Only took 3 min to create the table this time*

 
 10% done
![Image of 10 percent done](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/13_qp_create_cluster_table_step2_create_10.png)
 
 30% done
![Image of 30 percent done](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/14_qp_create_cluster_table_step2_create_20.png)
 
 80% done
![Image of 80 percent done](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/15_qp_create_cluster_table_step2_create_80.png)

 
 100% done
![Image of 100 percent done ](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/16_qp_create_cluster_table_step2_create_100.png)

### So let's take a look at the clustering now!  
 
I bet you expect the table to be clustered already...

![Alt Text](https://media.giphy.com/media/Wq9RLX06zRg4UM42Qf/giphy.gif)


An item to note is that since this table already has clustering added you don't have to enter in the column that you are getting for clustering info on it.  It will autmatically pick the clustered column. You can see the query below that is slightly different from the queries we used before to get the clustering information.  

```sql

SELECT SYSTEM$CLUSTERING_INFORMATION('MY_PRACTICE_DB.PRACTICE.CUSTOMER_CLUSTER')

```

As you can see the table is barely clustered. 
![Image of clustering imediate](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/19_clustering_info_cluster_market_4min.png)

![Alt Text](https://media.giphy.com/media/l2JhtKtDWYNKdRpoA/giphy.gif) 

Well because it takes time for a table to be optimized

 
 6min in 
![Image of clustering 6min](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/20_clustering_info_cluster_market_6min.png)

 
 8min in 
![Image of clustering 8min](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/21_clustering_info_cluster_market_8min.png)

 
 10min in 
![Image of clustering 10min](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/23_clustering_info_cluster_market_10min.png)

 
 15min in 
![Image of clustering 15 min](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/24_clustering_info_cluster_market_15min.png)

 
 20min in 
![Image of clustering 20min](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/25_clustering_info_cluster_market_20min.png)

 
 2 hrs in but it didn't improve 
 :-1:
:thumbsdown:

![Image of clustering 2hrs](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/26_clustering_info_cluster_market_2hr.png)

*It took 20 mins for the clustering to be added even though we clustered the table from the beginning*

**Question** What if we add clustering after the table is created?

### Reclustering an unclustered table


![Image of clustering added after table is created](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/36_alter_table_nocluster.png)

```sql

ALTER TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER CLUSTER BY (C_MKTSEGMENT);

```

![Image of clustering added after table is created](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/37_no_change_nocluster.png)

*Unfortunately this does not cause the table to be clustered imediately.  You sill still need to recluster the table.*

```sql

ALTER TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER RECLUSTER;

```

![Image of clustering added after table is created](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/40_qp_recluster.png)

*hmmm how long will this take now?*

![Image of reclustering time to complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/39_time_complete_recluster.png)

*It took about 6 min to recluster the table*

![Image of reclustered table](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/41_reclustered_table.png)


### Creating a table without clustering but having an order added

Let's create a table now without clustering but one that has the data sorted when added to the table.  

```sql

CREATE OR REPLACE TRANSIENT TABLE MY_PRACTICE_DB.PRACTICE.CUSTOMER_NOCLUSTER_ORDER
AS
SELECT * FROM SNOWFLAKE_SAMPLE_DATA.TPCH_SF10000.CUSTOMER
ORDER BY C_MKTSEGMENT;

```
*It will take longer to complete since the query is adding a sorting step* 

![Image of creating a no cluster table with order](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/29_qp_create_nocluster_order_sort.png)

![Image of creating a no cluster table with order](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/30_qp_create_nocluster_order_tablescan2.png)

![Image of creating a no cluster table with order](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/31_qp_create_nocluster_order_tablescan3.png)

![Alt Text](https://media.giphy.com/media/3oz8xKaR836UJOYeOc/giphy.gif)

![Image of creating a no cluster table with order](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/32_qp_create_nocluster_order_tablescan4.png)

![Image of creating a no cluster table with order took only 7min to complete](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/34_created_nocluster_order_7min.png)

*This table only took 7 min to complete and it is completely clustered* 

![Alt Text](https://media.giphy.com/media/lRnUWhmllPI9a/giphy.gif)

![Image of creating a no cluster table with order clustered](https://github.com/ferninphilly/developintelligence_snowflake/blob/master/Module_04/Lab_04/Images/35_clustering_info_nocluster_order_cluster_complete_imediate.png)

**Question** Which would you choose to use?




