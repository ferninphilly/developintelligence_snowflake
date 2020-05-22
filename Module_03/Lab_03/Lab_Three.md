# Lab Three

## Loading data into Snowflake

There are multiple ways to do most things in most code bases and Snowflake is no different. We're going to start with the web GUI and also go through the SQL method.

### Loading in the WEB GUI

SO...the first step is to create a database. Based on our last lesson let's go through all of the steps to create a database and a user to have access to that database. 

We're going to review each of these commands so please follow along:

```sql
USE ROLE ACCOUNTADMIN;
CREATE DATABASE mordor;
USE DATABASE mordor;
CREATE WAREHOUSE hobbit
    WAREHOUSE_SIZE = XSMALL
    ;
CREATE ROLE chosen_one;
GRANT USAGE ON DATABASE mordor TO ROLE chosen_one;
GRANT CREATE SCHEMA ON DATABASE mordor TO ROLE chosen_one;
GRANT ALL ON SCHEMA mordor.PUBLIC TO ROLE chosen_one;
CREATE or replace USER frodo PASSWORD='precious' DEFAULT_ROLE = 'chosen_one' DEFAULT_WAREHOUSE='mordor';
GRANT ROLE chosen_one TO USER frodo;
GRANT ALL ON WAREHOUSE hobbit TO chosen_one;

```

![frodo](./images/frodo.jpeg)

Okay...excellent. So we have our hero (FRODO) and we have our MORDOR. NOW...we need to fill it up with data! (umm....ORCS?)

![orcs](./images/orcs.jpeg)

OKAY! So the first thing we need to do is create a table to manage our data! 

Let's take a quick look at the data we'll be loading in here so that we have the metadata that we can use to create our table. Go ahead and open up (or download the lab data directly from github) the file in `developintelligence_snowflake/Module_03/Lab_03/lab_data/2020-03-devon-and-cornwall-street.csv`

Note the headers there. Let's go ahead and create the table to hold our "orc crimes".

```sql
CREATE TABLE mordor.PUBLIC.frodo (
    crime_id string,
    month string,
    reported_by string,
    falls_within string,
    longitude string,
    latitude string,
    location string,
    lsoa_code string,
    lsoa_name string,
    crime_type string,
    last_outcome_category string,
    context string
)

STAGE_FILE_FORMAT = (TYPE = 'csv' FIELD_DELIMITER= '/t')

```

NOW- notice the portion at the bottom: **STAGE_FILE_FORMAT**. Note that _this is going to be used to mark the primary methodology by which we will be loading data_
SO...in our case we're going to be using csv so, obviously, csv is the method. 

Okay...so first method of loading is going to be through the web GUI. The first thing you will want to do click on the "databases" icon next to the snowflake logo:

![dbscreen](./images/dbscreen.png)

Okay...from here we need to choose database **mordor** (where our table exists). _It is possible that you do not see this listed in your databases!_

### CHALLENGE ONE: IF YOU DON'T SEE THE DATABASE HERE...WHERE COULD YOU GO TO SEE IT? DOES IT EXIST? (ANSWER BELOW)

Hint: Switch roles to the role that would allow you to see **all** warehouses

Okay...so choose DATABASES -> MORDOR -> TABLES

Choose FRODO and you should see a screen like this

![tablescreen](./images/tablescreen.png)

Once you choose the table you will see a list of columns. Let's choose "LOAD TABLE" from the section under the __Tables__ mark. Click on that. First question: Which warehouse? (HOBBIT)

![warehousechoice](./images/warehousechoice.png)

Select the file in **lab_data** in this module (assuming you've downloaded this git repo) called **./developintelligence_snowflake/Module_03/Lab_03/lab_data/2020-03-devon-and-cornwall-street.csv**. LET'S LOAD THOSE ORC CRIMES

**IT'S POSSIBLE** that you might need to create a new method for csv (not guaranteed) where CSV is the default. Go ahead and do that. 

Once loaded return to your main screen and run `SELECT * FROM mordor.public.frodo` to see the data.

![dataloaded](./images/dataloaded.png)

### LOADING FROM THE COMMAND LINE

OKAY! SO let's see if we can log in from our command line. 

### CHALLENGE TWO: GOING BACK TO THE FIRST LAB- UPDATE THE ~/.snowsql/config FILE AND LOG IN TO THE MORDOR DATABASE WITH USER FRODO. YOU WILL NEED TO REFERENCE BACK TO LAB ONE AND THE CODE ABOVE.

