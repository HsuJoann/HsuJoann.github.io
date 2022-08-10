---
tags: pySpark
---

## When SQL Meets Spark 1 –connect PySpark to SQL Server
Today, I will start a new series of blogs about Spark. Same as in the Series of Python, I will share my journey of becoming a big data developer from a SQL developer. One year ago, in one of my favorite local SQL Server meeting, I suddenly got the aha moment. From there, I have run in a fast track on big data coding. Now, I am the lead developer of one of our project based on data in Hadoop. I will share my commonly used codes so that you can start your journey faster.

At that aha moment, I saw through all the unfamiliar and intimidating jargons of Hadoop ecosystem, and realized this fact—-the core of Spark is dataframe (or RDD depends on where you stand and what tool you choose)  just like the core of SQL is table. For anything we can do with table in SQL world, we can and need to achieve in PySpark world. This insight acts like a map for me to effortlessly explore and exploit the Hadoop world. So, I will designate this series as “to Hadoop and beyond”

OK. Let’s go to details of coding (Since my home computer has no big data cluster, the codes that I posted here was not literally tested. But, they are pretty good and makes sense)

Now we know that the core of PySpark is dataframe. Our goal is to get a dataframe from SQL server query. First google “PySpark connect to SQL Server”.  I copied the code from this page without any change because I can test it anyway.
(  https://www.sqlrelease.com/read-and-write-data-to-sql-server-from-spark-using-pyspark )


```python
#import required modules
from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession
 
#Create spark configuration object
conf = SparkConf()
conf.setMaster("local").setAppName("My app")
 
#Create spark context and sparksession
sc = SparkContext.getOrCreate(conf=conf)
spark = SparkSession(sc)
#set variable to be used to connect the database
database = "TestDB"
table = "dbo.tbl_spark_df"
user = "test"
password  = "*****"
 
#read table data into a spark dataframe
jdbcDF = spark.read.format("jdbc") \
    .option("url", f"jdbc:sqlserver://localhost:1433;databaseName={database};") \
    .option("dbtable", table) \
    .option("user", user) \
    .option("password", password) \
    .option("driver", "com.microsoft.sqlserver.jdbc.SQLServerDriver") \
    .load()
 
#show the data loaded into dataframe
jdbcDF.show()

```
There are two important things that this code did not touch. First: Jar file and parameter set up. Since I use PyCharm to set the parameters. So, I have not tried this found on this page:

```python
conf = SparkConf() \
    .setAppName(appName) \
    .setMaster(master) \
    .set("spark.driver.extraClassPath","sqljdbc_7.2/enu/mssql-jdbc-7.2.1.jre8.jar")
Second: in real life, I rarely move a whole table from SQL server to a dataframe. More often, I used a complicated query and code will be like this:

#import required modules
from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession
 
#Create spark configuration object
conf = SparkConf()
conf.setMaster("local").setAppName("My app")
 
#Create spark context and sparksession
sc = SparkContext.getOrCreate(conf=conf)
spark = SparkSession(sc)
#set variable to be used to connect the database
database = "TestDB"
user = "test"
password  = "*****"

SQL_query = ''' ( select column1,
                column1
               from %s join %s and so on) query'''%('table1', 'table2')
 
#read table data into a spark dataframe
jdbcDF = spark.read.format("jdbc") \
    .option("url", f"jdbc:sqlserver://localhost:1433;databaseName={database};") \
    .option("dbtable", SQL_query) \
    .option("user", user) \
    .option("password", password) \
    .option("driver", "com.microsoft.sqlserver.jdbc.SQLServerDriver") \
    .load()
 
#show the data loaded into dataframe
jdbcDF.show()
```
At the second half of this page, you can find the code that write a dataframe to SQL Server. Now, we can get dataframe from and to SQL server. I will talk about how to get dataframe from a file, from Hive table, etc in my next post. In the third post in this series, I will share codes and thoughts about how to manipulate dataframes.
