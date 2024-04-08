# Assignment 1 - Hive

1. Download vechile sales data from -> https://github.com/rizal-muhammed/Learning-Hive/blob/main/Data/sales_order_data.csv
2. Store raw data into hdfs location
```
hadoop fs -put /tmp/Hive/sales_order_data.csv /tmp/Hive/
```
<br>

3. Create a internal hive table "sales_order_table" which will store csv data sales_order_data.csv. make sure to skip header row while creating table
4. Load data from hdfs path into "sales_order_csv" 
```
hive> CREATE DATABASE IF NOT EXISTS assignment1
    >   COMMENT "Database to solve Assignment 1";
hive> USE assignment1;

hive> CREATE TABLE IF NOT EXISTS sales_order_table (
    > ORDERNUMBER INT,
    > QUANTITYORDERED INT,
    > PRICEEACH FLOAT,
    > ORDERLINENUMBER INT,
    > SALES FLOAT,
    > STATUS STRING,
    > QTR_ID INT,
    > MONTH_ID INT,
    > YEAR_ID INT,
    > PRODUCTLINE STRING,
    > MSRP INT,
    > PRODUCTCODE STRING,
    > PHONE STRING,
    > CITY STRING,
    > STATE STRING,
    > POSTALCODE STRING,
    > COUNTRY STRING,
    > TERRITORY STRING,
    > CONTACTLASTNAME STRING,
    > CONTACTFIRSTNAME STRING,
    > DEALSIZE STRING
    > )
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > TBLPROPERTIES ('skip.header.line.count' = "1");

hive> LOAD DATA INPATH 'hdfs:///sales_data/sales_order_data.csv'
    > OVERWRITE INTO TABLE sales_order_table;
```
<br>


5. Create an internal hive table which will store data in ORC format "sales_order_table_orc"
6. Load data from "sales_order_table" into "sales_order_table_orc"
```
hive> CREATE TABLE IF NOT EXISTS sales_order_table_orc (
    > ORDERNUMBER INT,
    > QUANTITYORDERED INT,
    > PRICEEACH FLOAT,
    > ORDERLINENUMBER INT,
    > SALES FLOAT,
    > STATUS STRING,
    > QTR_ID INT,
    > MONTH_ID INT,
    > YEAR_ID INT,
    > PRODUCTLINE STRING,
    > MSRP INT,
    > PRODUCTCODE STRING,
    > PHONE STRING,
    > CITY STRING,
    > STATE STRING,
    > POSTALCODE STRING,
    > COUNTRY STRING,
    > TERRITORY STRING,
    > CONTACTLASTNAME STRING,
    > CONTACTFIRSTNAME STRING,
    > DEALSIZE STRING
    > )
    > ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    > STORED AS ORC
    > TBLPROPERTIES ("skip.header.line.count" = "1");

hive> INSERT OVERWRITE TABLE sales_order_table_orc
    > SELECT * FROM sales_order_table;
```

## Perform below mentioned queries on "sales_order_table_orc" table :
1. Calculate total sales per year
```
hive> SELECT SUM(SALES) AS YEARLY_SALES
    > FROM sales_order_table_orc
    > GROUP BY YEAR_ID;
```
<br>

2. Find a product for which maximum orders were placed
```
hive> SELECT PRODUCTLINE AS STAR_PRODUCT, QUANTITYORDERED
    > FROM sales_order_table_orc
    > ORDER BY QUANTITYORDERED DESC
    > LIMIT 1;
```
<br>

3. Calculate the total sales for each quarter
```
hive> SELECT QTR_ID AS QTR, SUM(SALES) AS QTR_SALES
    > FROM sales_order_table_orc
    > GROUP BY QTR_ID;
```
<br>

4. In which quarter sales was minimum
```
hive> SELECT QTR_ID AS QTR, SUM(SALES) AS QTR_SALES
    > FROM sales_order_table_orc
    > GROUP BY QTR_ID
    > ORDER BY QTR_SALES ASC LIMIT 1;
```
<br>

5. In which country sales was maximum and in which country sales was minimum
```
hive> SELECT COUNTRY AS MAX_COUNTRY, SALES
    > FROM sales_order_table_orc
    > ORDER BY SALES DESC
    > LIMIT 1;

hive> SELECT COUNTRY AS MIN_COUNTRY, SALES
    > FROM sales_order_table_orc
    > ORDER BY SALES ASC
    > LIMIT 1;
```
<br>

6. Calculate quarterly sales for each city
```
hive> SELECT CITY, QTR_ID AS QTR, SUM(SALES) AS QTR_SALES
    > from sales_order_table_orc
    > GROUP BY CITY, QTR_ID;
```
<br>

7. Find a month for each year in which maximum number of quantities were sold
```
hive> SELECT YEAR_ID AS YEAR,
    >        MONTH_ID AS MONTH,
    >        MAX(QUANTITYORDERED) AS MAX_QTY
    > from sales_order_table_orc
    > GROUP BY YEAR_ID, MONTH_ID;
```
<br>

