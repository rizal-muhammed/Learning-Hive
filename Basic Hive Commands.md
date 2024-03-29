# Basic Hive Commands

**Create a Database**
[create database reference](https://cwiki.apache.org/confluence/display/hive/languagemanual+ddl#LanguageManualDDL-Create/Drop/Alter/UseDatabase)
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
[create table reference](https://cwiki.apache.org/confluence/display/hive/languagemanual+ddl#LanguageManualDDL-CreateTableCreate/Drop/TruncateTable)
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

**Loading data into table from local**
[Load data local reference](https://cwiki.apache.org/confluence/display/hive/languagemanual+dml#LanguageManualDML-Loadingfilesintotables)
```
[cloudera@quickstart ~]$ hive
hive> USE database1;
hive> SHOW TABLES;
OK
department_data

hive> LOAD DATA LOCAL INPATH 'file:///tmp/Hive/department_data.csv'
    > INTO TABLE department_data;

hive> SET hive.cli.print.header=true;
hive> SELECT * FROM department_data LIMIT 10;
OK
department_data.dept_id	department_data.dept_name	department_data.manager_id	department_data.salary
10	Administration	200	1700
20	Marketing	201	1800
30	Purchasing	114	1700
40	Human Resources	203	2400
50	Shipping	121	1500
60	IT	103	1400
70	Public Relations	204	2700
80	Sales	145	2500
90	Executive	100	1700
100	Finance	108	1700
```
<br>

Similarly we can load data into internal table from hdfs location as follows  
  
**copy file/files to hdfs location**
```
[cloudera@quickstart ~]$ hadoop fs -mkdir /tmp/Hive
[cloudera@quickstart ~]$ hadoop fs -put /tmp/Hive/department_data.csv /tmp/Hive
```
**load data into table from hdfs location**
```
[cloudera@quickstart ~]$ hive
hive> USE database1;

hive> CREATE TABLE IF NOT EXISTS department_data_from_hdfs
    > (
    >      dept_id INT,
    >     dept_name STRING,
    >      manager_id INT,
    >      salary INT
    > )
    > COMMENT 'Information about department data will loaded from hdfs, to this table'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ',';
OK
hive> LOAD DATA INPATH '/tmp/Hive/'
    > INTO TABLE department_data_from_hdfs;

hive> set hive.cli.print.header = true;
hive> SELECT * FROM department_data_from_hdfs
    > LIMIT 10;
OK
department_data_from_hdfs.dept_id	department_data_from_hdfs.dept_name	department_data_from_hdfs.manager_id	department_data_from_hdfs.salary
10	Administration	200	1700
20	Marketing	201	1800
30	Purchasing	114	1700
40	Human Resources	203	2400
50	Shipping	121	1500
60	IT	103	1400
70	Public Relations	204	2700
80	Sales	145	2500
90	Executive	100	1700
100	Finance	108	1700
```
<br>

## Creating External Table and pointing data to it
copy data into hdfs location  
create external table, mentioning this hdfs location  
```
[cloudera@quickstart ~]$ hadoop fs -put /tmp/Hive/department_data.csv /tmp/Hive/
[cloudera@quickstart ~]$ hive

Logging initialized using configuration in file:/etc/hive/conf.dist/hive-log4j.properties
WARNING: Hive CLI is deprecated and migration to Beeline is recommended.
hive> USE database1;
OK
Time taken: 0.441 seconds
hive> SHOW TABLES;
OK
department_data
department_data_from_hdfs
Time taken: 0.295 seconds, Fetched: 2 row(s)
hive> CREATE EXTERNAL TABLE IF NOT EXISTS department_data_external (
    > dept_id INT,
    > dept_name STRING,
    > manager_id INT,
    > salary INT
    > )
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > LOCATION '/tmp/Hive/';
OK
Time taken: 0.18 seconds
hive> set hive.cli.print.header = true;
hive> select * from department_data_external limit 5;
OK
department_data_external.dept_id	department_data_external.dept_name	department_data_external.manager_id	department_data_external.salary
10	Administration	200	1700
20	Marketing	201	1800
30	Purchasing	114	1700
40	Human Resources	203	2400
50	Shipping	121	1500
Time taken: 0.462 seconds, Fetched: 5 row(s)
```
<br>

**Dealing with collection items in Hive**
```
[cloudera@quickstart Data]$ hive
hive> use database1;
OK
Time taken: 0.632 seconds
hive> CREATE TABLE IF NOT EXISTS employee (
    > id INT,
    > name STRING,
    > skills ARRAY<STRING>
    > )
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > COLLECTION ITEMS TERMINATED BY ':'
    > ;
OK
Time taken: 1.109 seconds
hive> LOAD DATA LOCAL INPATH 'file:///tmp/Hive/array_data.csv'
    > INTO TABLE employee;
Loading data to table database1.employee
Table database1.employee stats: [numFiles=1, totalSize=108]
OK
Time taken: 1.364 seconds
hive> set hive.cli.print.header = true;
hive> select * from employee limit 5
    > ;
OK
employee.id	employee.name	employee.skills
101	Amit	["Hadoop","Hive","Spark","Big-Data"]
102	Sumit	["Hive","Ozzie","Hadoop","Spark","Storm"]
103	Rohit	["Kafka","Cassandra","Hbase"]

hive> select name, skills[0] as prime_skill
    > from employee
    > limit 5;
OK
name	prime_skill
Amit	Hadoop
Sumit	Hive
Rohit	Kafka
```
<br>

**collection functions**  
[collection functions reference](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-CollectionFunctions)  
example:  
```
hive> select name, size(skills) as num_of_skills, array_contains(skills, 'Hadoop') as knows_hadoop
    > from employee
    > limit 5;
OK
name	num_of_skills	knows_hadoop
Amit	4	true
Sumit	5	true
Rohit	3	false
```
