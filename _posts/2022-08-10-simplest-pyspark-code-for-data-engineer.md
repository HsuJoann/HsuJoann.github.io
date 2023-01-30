---
tags: pySpark
---

## Simplest PySpark Code for Data Engineer

A data Engineer is a person who moved huge amount of data between various data sources. The the volumn of data gets bigger, use our computer is not good enough and can not be scheduled. This blog post is to show you how to use a hadoop cluster as a resource for transfering data between two sql servers. The tool is PySpark.

```python
from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession

# Create spark configuration object
conf = SparkConf().setAppName('JJHSU_app_test').setMaster('yarn')
conf.set('spark.driver.memory', '10g')
conf.set('spark.executor.memory', '10g')
conf.set("spark.sql.broadcastTimeout",  1200)
conf.set("spark.sql.execution.arrow.enabled", "true")
conf.set('spark.yarn.queue', 'hero')
spark = SparkSession.builder.config(conf=conf).enableHiveSupport().getOrCreate()
spark.sparkContext.setLogLevel('ERROR')
sc = spark.sparkContext

# set variable to be used to connect the database
database0 = "SQLServer_DataBase_name"
table0 = "Schema_name"
user0 = "User_id"
password0 = "password"



# read table data from source database into a spark dataframe
jdbcDF = spark.read.format("jdbc") \
    .option("url", f"jdbc:sqlserver://Server_nme_Port;databaseName={database0};") \
    .option("dbtable", table0) \
    .option("user", user0) \
    .option("password", password0) \
    .option("driver", "com.microsoft.sqlserver.jdbc.SQLServerDriver") \
    .load() \
    .cache()



#write dataframe into Target_base
database = "Target_DB"
table = "Targe_table_name"
user = "User_id_Target"
password = "Password_target"





jdbcDF.write.format('jdbc').option('url',f"jdbc:sqlserver://ServerPlusPort;databaseName={database};") \
    .option('dbtable', table) \
    .option("user", user) \
    .option("password", password) \
    .option('driver', "com.microsoft.sqlserver.jdbc.SQLServerDriver")\
    .save()

print('DONE')


```

Hope this code works well for you.
