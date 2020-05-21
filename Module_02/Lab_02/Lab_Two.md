# LAB TWO

## Creating Warehouses

As we went over in the first section of our lecture portion- WAREHOUSES are the highest level of data storage arena that you have available in snowflake. 
Fortunately they are DEAD EASY to create BUT...you have to remember how the system works. 
Not just **anyone** can create a deathstar...er....warehouse. 

![palpatine](./images/palpatine.jpeg)

SO FIRST we must become the sith Lord himself with:
`USE ROLE ACCOUNTADMIN`

Go ahead and put that into your snowflake GUI. 
NOW we are ready to build our death star. Go ahead and create the warehouse with this very simple command:

`CREATE WAREHOUSE deathstar WAREHOUSE_SIZE=XSMALL`

Assuming that that was successful you should be able to run `SHOW WAREHOUSES` and see DEATHSTAR is now a warehouse.
Excellent. Now let the hate flow through you for a while before we move on to the next step.

![hateflowthroughyou](./images/hateflowthroughyou.jpeg)

Okay...so now we have a WAREHOUSE...the next thing that we'll want to do is have a **database** to operate **within** that warehouse. Easy enough but FIRST we _must_ remember to USE deathstar so please make sure we:

`USE WAREHOUSE deathstar`

Followed by...

`CREATE DATABASE throneroom`

Excellent! Okay...so we have our WAREHOUSE. We have our DATABASE WITHIN that warehouse now let's start creating users!

## Managing Users

So- as we've gone over in the lab- one of the first things we need to do when setting up a new SNOWFLAKE system is to create a user with a LOT of permissions (but not ALL of the permissions). As a quick reminder- these are the default roles:

* ACCOUNTADMIN
* SECURITYADMIN
* SYSADMIN
* PUBLIC

Now- the important thing to note here is that the **PUBLIC** role is more like a pseudorole that really doesn't do anything. It's basically DEFAULT. 

SO...let's get a look at our roles with the simple command: `SHOW ROLES`. Run that now. 
You should see a table listing the generic roles. Fine. Let's create a role now. 

Run `CREATE ROLE "darth_vader"`;

IF you run into issues with permissions allowing you to create the role you will need to upgrade YOUR user's role to `ACCOUNTADMIN`. Go ahead and make that happen.

NOW...we have four options (kind of think of them as "templates") that we can make this role part of. Let's do

`GRANT ROLE "darth_vader" to ROLE "SECURITYADMIN"`

### CHALLENGE ONE: WHY ARE WE USING SECURITYADMIN HERE? WHAT DOES THIS ROLE ACCOMPLISH??

You've just handed over power. 

![power](./images/power.jpeg)

Okay...let's make sure that this role appears in our list. 
Run `SHOW ROLES` again. 
Ideally you should see your darth_vader role there.

![vader](./images/vader.jpeg)

Okay...so now we've created a role...we need to add privileges to him! So which of these powers should we grant to Darth?

| ALL [ PRIVILEGES ]   | All                                                    | Grants all the privileges for the specified object type.                                                                                                                                                                                                                                                                                                                      |   |   |
|----------------------|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| CREATE <object_type> | Global , Database , Schema                             | Grants ability to create an object of <object_type> (e.g. CREATE TABLE grants the ability to create a table within a schema).                                                                                                                                                                                                                                                 |   |   |
| CREATE ACCOUNT       | Global                                                 | Grants ability to create managed accounts; currently applies only to data providers creating reader accounts for sharing data with consumers.                                                                                                                                                                                                                                 |   |   |
| CREATE SHARE         | Global                                                 | Grants ability to create shares; applies to data providers for sharing data with other accounts.                                                                                                                                                                                                                                                                              |   |   |
| DELETE               | Table                                                  | Grants ability to execute a DELETE command on the table.                                                                                                                                                                                                                                                                                                                      |   |   |
| EXECUTE TASK         | Global                                                 | Grants ability to run any tasks owned by the role.                                                                                                                                                                                                                                                                                                                            |   |   |
| IMPORT SHARE         | Global                                                 | Grants ability to view shares shared with your account and create databases from the shares; applies to data consumers.                                                                                                                                                                                                                                                       |   |   |
| IMPORTED PRIVILEGES  | Table                                                  | Grants ability to enable roles other than the owning role to access a shared database; applies only to shared databases.                                                                                                                                                                                                                                                      |   |   |
| INSERT               | Table                                                  | Grants ability to execute an INSERT command on the table.                                                                                                                                                                                                                                                                                                                     |   |   |
| MANAGE GRANTS        | Global                                                 | Grants ability to grant or revoke privileges on any object as if the invoking role were the owner of the object.                                                                                                                                                                                                                                                              |   |   |
| MODIFY               | Resource Monitor , Warehouse , Database , Schema       | Grants ability to change the settings or properties of an object (e.g. on a virtual warehouse, provides the ability to change the size of a virtual warehouse).                                                                                                                                                                                                               |   |   |
| MONITOR              | Resource Monitor , Warehouse , Database , Schema, Task | Grants ability to see details within an object (e.g. queries and usage within a warehouse).                                                                                                                                                                                                                                                                                   |   |   |
| MONITOR EXECUTION    | Global                                                 | Grants ability to monitor runs of any tasks in the account.                                                                                                                                                                                                                                                                                                                   |   |   |
| MONITOR USAGE        | Global                                                 | Grants ability to monitor account-level usage and historical information for databases and warehouses; for more details, see Enabling Non-Account Administrators to Monitor Usage and Billing History. Additionally grants ability to view managed accounts using SHOW MANAGED ACCOUNTS.                                                                                      |   |   |
| OPERATE              | Warehouse, Task                                        | Grants ability to start, stop, suspend, or resume a virtual warehouse. Grants ability to suspend or resume a task.                                                                                                                                                                                                                                                            |   |   |
| OWNERSHIP            | All                                                    | Grants ability to delete, alter, and grant or revoke access to an object. Required to rename an object. OWNERSHIP is a special privilege on an object that is automatically granted to the role that created the object, but can also be transferred using the GRANT OWNERSHIP command to a different role by the owning role (or any role with the MANAGE GRANTS privilege). |   |   |
| REFERENCES           | Table                                                  | Grants ability to reference a table as the unique/primary key table for a foreign key constraint.                                                                                                                                                                                                                                                                             |   |   |
| SELECT               | Table , External table, View, Stream                   | Grants ability to execute a SELECT statement on the table/view.                                                                                                                                                                                                                                                                                                               |   |   |
| TRUNCATE             | Table                                                  | Grants ability to execute a TRUNCATE TABLE command on the table.                                                                                                                                                                                                                                                                                                              |   |   |
| UPDATE               | Table                                                  | Grants ability to execute an UPDATE command on the table.                                                                                                                                                                                                                                                                                                                     |   |   |
| USAGE                | Warehouse , Database , Schema                          | Grants ability to execute a USE <object> command on the object; also grants ability to execute a SHOW <objects> command on the objects within a database or schema; however, a contained object is only listed in the output if the executing role also has at least one privilege on the object.                                                                             |   |   |.

SO...let's go ahead and add some privileges to our role. The first thing we want ot do with this is to allow our new role to **operate** on our warehouse.

Basically- we want darth to be pretty powerful sooo....

`GRANT operate ON warehouse deathstar to role darth_vader`

AND let's give his ROLE privileges on our PUBLIC schema so...

`GRANT ALL ON DATABASE throneroom to role darth_vader`

`GRANT ALL ON SCHEMA throneroom.public to ROLE darth_vader`

OKAY! Awesome! So now we have our ROLE defined.
So remember from the lecture portion that Snowflake is very ROLE based and not USER based (which is different)

![access-control-relationships](./images/access-control-relationships.png)

Before we go on let's take a look at the summary here! IF you find yourself getting lost here you can always run `SHOW GRANTS TO ROLE darth_vader` (go ahead and do this now)

ALSO if you want to get a look at the ROLE situations with any objects in the hierarchy you can run this to find now (do it now):

`SHOW GRANTS ON DATABASE throneroom`

NOW let's go on and create a USER and assign them to the role. Another nice thing about SNOWFLAKE is that the commands are pretty straightforward...so let's take a look at one:

`CREATE USER lukeskywalker PASSWORD='tatooinerocks' DEFAULT_ROLE = "public";`

![skywalker](./images/skywalker.jpeg)

Now we want to grant our new user LUKE some serious power. Let's give him the role of DARTH VADER (this was the emperor's plan so let's bring it to fruition):

`GRANT ROLE darth_vader TO USER lukeskywalker`

WAIT! NO!!! HE'S GOING TO BETRAY US!!

`REVOKE ROLE darth_vader FROM USER lukeskywalker`

Meh. Let's trust him again...

`GRANT ROLE darth_vader TO USER lukeskywalker`

AND let's check to make sure that we have successfully updated the user:

`SHOW GRANTS TO USER lukeskywalker`

Did you get that he now has DARTH_VADER powers? 

![palpatineplan](./images/palpatineplan.jpeg)

Now that you have successfully crushed the rebellion it's time to remove everything we've done. Fortunately, again, Snowflake makes this pretty easy. Let's go through and run each of these:

```sql
DROP DATABASE throneroom
DROP ROLE darth_vader
DROP USER lukeskywalker
DROP WAREHOUSE deathstar 

```

Damn. Rebels win again...

![deathstar](./images/deathstar.jpeg)

### CHALLENGE TWO: CREATE A NEW WAREHOUSE, DATABASE, AND SCHEMA. CREATE A ROLE THAT HAS SELECT AND INSERT PERMISSIONS ON THAT SCHEMA. SHOW ALL OF THE GRANTS THAT YOU HAVE ON THESE OBJECTS. SEND COMMANDS TO INSTRUCTOR WHEN DONE ON CHAT

### HEAD BACK TO LECTURE PORTION NOW