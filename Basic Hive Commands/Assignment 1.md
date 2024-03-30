## Assingnment 1
Getting familiar with Hive tables  
refer [here](https://github.com/rizal-muhammed/Learning-Hive/blob/main/Data/covid_19.csv) for data
<br>

**Creating database and table**
```
hive> CREATE DATABASE IF NOT EXISTS covid_database
    > COMMENT 'Covid database'
    > WITH DBPROPERTIES (
    >     'owner'='Rizal Muhammed',
    >     'created_date'='30/03/2024',
    >     'purpose'='Collection of information about Covid-19'
    > );

hive> CREATE TABLE IF NOT EXISTS covid_data (
    > province STRING,
    > country STRING,
    > latitude FLOAT,
    > longitude FLOAT,
    > date DATE,
    > confirmed BIGINT,
    > deaths BIGINT,
    > recovered BIGINT,
    > active BIGINT,
    > WHO_region STRING
    > )
    > COMMENT 'Covid-19 information'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > TBLPROPERTIES("skip.header.line.count"="1");

```
<br>

**Load data from local path**
```
hive> LOAD DATA LOCAL INPATH 'file:///tmp/Hive/covid_19.csv'
    > INTO TABLE covid_data;

hive> set hive.cli.print.header = true;
hive> select * from covid_data limit 10;
OK
covid_data.province	covid_data.country	covid_data.latitude	covid_data.longitude	covid_data.date	covid_data.confirmed	covid_data.deaths	covid_data.recovered	covid_data.active	covid_data.who_region
	Afghanistan	33.93911	67.70995	2020-01-22	0Eastern Mediterranean
	Albania	41.1533	20.1683	2020-01-22	0	0	0	0Europe
	Algeria	28.0339	1.6596	2020-01-22	0	0	0	0Africa
	Andorra	42.5063	1.5218	2020-01-22	0	0	0	0Europe
	Angola	-11.2027	17.8739	2020-01-22	0	0	0Africa
	Antigua and Barbuda	17.0608	-61.7964	2020-01-22	0Americas
	Argentina	-38.4161	-63.6167	2020-01-22	0Americas
	Armenia	40.0691	45.0382	2020-01-22	0	0	0	0Europe
Australian Capital Territory	Australia	-35.4735	149.0124	2020-01-22	0	0	0	0	Western Pacific
New South Wales	Australia	-33.8688	151.2093	2020-01-2Western Pacific
```
<br>

**Load data from hdfs location**
```
[cloudera@quickstart ~]$ hadoop fs -mkdir /tmp/Hive/Covid_19
[cloudera@quickstart ~]$ hadoop fs -put /tmp/Hive/covid_19.csv /tmp/Hive/Covid_19/

[cloudera@quickstart ~]$ hive
hive> USE covid_database;
OK
Time taken: 0.456 seconds
hive> CREATE TABLE IF NOT EXISTS covid_data_from_hdfs (
    > province STRING,
    > country STRING,
    > latitude FLOAT,
    > longitude FLOAT,
    > date DATE,
    > confirmed BIGINT,
    > deaths BIGINT,
    > recovered BIGINT,
    > active BIGINT,
    > WHO_region STRING
    > )
    > COMMENT 'Covid-19 information loading from hdfs'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > TBLPROPERTIES("skip.header.line.count"="1");
OK
hive> LOAD DATA INPATH '/tmp/Hive/Covid_19/'
    > INTO TABLE covid_data_from_hdfs;

hive> set hive.cli.print.header = true;
hive> select * from covid_data_from_hdfs limit 5;
OK
covid_data_from_hdfs.province	covid_data_from_hdfs.country	covid_data_from_hdfs.latitude	covid_data_from_hdfs.longitude	covid_data_from_hdfs.date	covid_data_from_hdfs.confirmed	covid_data_from_hdfs.deaths	covid_data_from_hdfs.recovered	covid_data_from_hdfs.active	covid_data_from_hdfs.who_region
	Afghanistan	33.93911	67.70995	2020-01-22	0	0	0	Eastern Mediterranean
	Albania	41.1533	20.1683	2020-01-22	0	0	0	0	Europe
	Algeria	28.0339	1.6596	2020-01-22	0	0	0	0	Africa
	Andorra	42.5063	1.5218	2020-01-22	0	0	0	0	Europe
	Angola	-11.2027	17.8739	2020-01-22	0	0	0	0	Africa
```
<br>

**Creating an external table**
```
[cloudera@quickstart ~]$ hadoop fs -put /tmp/Hive/covid_19.csv /tmp/Hive/Covid_19/
[cloudera@quickstart ~]$ hive
hive> USE covid_database;

hive> CREATE EXTERNAL TABLE IF NOT EXISTS covid_data_external (
    > province STRING,
    > country STRING,
    > latitude FLOAT,
    > longitude FLOAT,
    > date DATE,
    > confirmed BIGINT,
    > deaths BIGINT,
    > recovered BIGINT,
    > active BIGINT,
    > WHO_region STRING
    > )
    > COMMENT 'Covid-19 information, external table'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > LOCATION '/tmp/Hive/Covid_19/'
    > TBLPROPERTIES("skip.header.line.count"="1");
OK

hive> set hive.cli.print.header = true;
hive> select * from covid_data_external limit 5;
OK
covid_data_external.province	covid_data_external.country	covid_data_external.latitudcovid_data_external.longitude	covid_data_external.date	covid_data_external.confirmed	covid_data_external.deaths	covid_data_external.recovered	covid_data_external.active	covid_data_external.who_region
	Afghanistan	33.93911	67.70995	2020-01-22	0	0	0	Eastern Mediterranean
	Albania	41.1533	20.1683	2020-01-22	0	0	0	0	Europe
	Algeria	28.0339	1.6596	2020-01-22	0	0	0	0	Africa
	Andorra	42.5063	1.5218	2020-01-22	0	0	0	0	Europe
	Angola	-11.2027	17.8739	2020-01-22	0	0	0	0	Africa
```
