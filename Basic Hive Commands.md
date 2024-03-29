# Basic Hive Commands

**Create a Database**
```
[cloudera@quickstart ~]$ hive
hive> CREATE DATABASE IF NOT EXISTS database1
    > COMMENT 'An example database to learn hive'
    > WITH DBPROPERTIES (
    >     'owner'='Rizal Muhammed',
    >     'created_data'='29/03/2024',
    >     'purpose'='An example database to explore hive'
    > );
hive> SHOW DATABASES;
OK
database1
hive> USE database1;
OK
```
<br>

**Create an Internal(Managed) Table**
```
hive> CREATE TABLE IF NOT EXISTS department_data
    > (
    >     dept_id INT,
    >     dept_name STRING,
    >     manager_id INT,
    >     salary INT
    > )
    > COMMENT 'This table stores information about department data'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ',';
OK

hive> SHOW TABLES;
OK
department_data
hive> DESCRIBE FORMATTED department_data;
OK
# col_name            	data_type           	comment             
	 	 
dept_id             	int                 	                    
dept_name           	string              	                    
manager_id          	int                 	                    
salary              	int                 	                    
	 	 
# Detailed Table Information	 	 
Database:           	database1           	 
Owner:              	cloudera            	 
CreateTime:         	Fri Mar 29 04:01:20 PDT 2024	 
LastAccessTime:     	UNKNOWN             	 
Protect Mode:       	None                	 
Retention:          	0                   	 
Location:           	hdfs://quickstart.cloudera:8020/user/hive/warehouse/database1.db/department_data	 
Table Type:         	MANAGED_TABLE       	 
Table Parameters:	 	 
	comment             	This table stores information about department data
	transient_lastDdlTime	1711710080          
	 	 
# Storage Information	 	 
SerDe Library:      	org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe	 
InputFormat:        	org.apache.hadoop.mapred.TextInputFormat	 
OutputFormat:       	org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat	 
Compressed:         	No                  	 
Num Buckets:        	-1                  	 
Bucket Columns:     	[]                  	 
Sort Columns:       	[]                  	 
Storage Desc Params:	 	 
	field.delim         	,                   
	serialization.format	,

hive> exit;
[cloudera@quickstart ~]$ hadoop fs -ls /user/hive/warehouse/database1.db
Found 1 items
drwxrwxrwx   - cloudera supergroup          0 2024-03-29 04:01 /user/hive/warehouse/database1.db/department_data
```
<br>

