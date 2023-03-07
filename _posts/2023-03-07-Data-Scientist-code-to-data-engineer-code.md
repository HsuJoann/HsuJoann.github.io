---
tags: Data Engineer and Architecture
---

## How to turn Data Scientists' python code to a Data Engineer PySpark code

Data Scientists are busy at trying differenct machine learning method and fine-tuning different parameters to achieve the best balance between different types of errors. 
After they find the best set, it is data engineer's turn to make the code run most efficiently on production scale. While most data scientists use Panda Dataframe for their code,
data engnieer use pyspark for large scale distribution computing. 
This blog shares my experience and code to make the bridge.

First, get data ready in PySpark DataFrame. 
Second, turn this into a special Dataframe for distribution. Each row is used to call Data Science Model related function. Each cell is a list
Three, Define the function: inside function,  tech prep, then turn the list into a panda' dataframe. Finally, get and use the code from  data scientist
Fourth step is to call the function 
Last, the the return list to a PySpark Datafrom


```python

from pyspark import SparkConf
from pyspark.sql import SparkSession,SQLContext
from pyspark.sql import Row
import pyspark.sql.functions as func
# import config.config_properties as config
# import config_properties as config
import sys
from datetime import date
from datetime import datetime
from dateutil.relativedelta import relativedelta
import pandas as pd


conf = SparkConf().setAppName('your_app_name').setMaster('yarn')
conf.set('spark.yarn.queue', 'queue_name').set("spark.yarn.executor-memory", "10G"). \
    set("spark.yarn.driver-memory", "20G"). \
    set("spark.driver.memory", "20G"). \
    set("spark.driver.maxResultSize", "20G"). \
    set("spark.executor.memoryOverhead", "10G"). \
    set("spark.driver.memoryOverhead", "20G"). \
    set("spark.sql.execution.arrow.enabled", "true"). \
    set("spark.sql.broadcastTimeout", "3600").set("spark.port.maxRetries", "50").set("spark.network.timeout", "800").\
    set("spark.sql.shuffle.partitions", "5001").set("spark.shuffle.registration.timeout", "20000").\
    set("spark.dynamicAllocation.executorIdleTimeout", "600s"). set("spark.dynamicAllocation.maxExecutors", "400").\
    set("spark.dynamicAllocation.minExecutors", "100").set("spark.shuffle.registration.timeout", "50000")
spark = SparkSession.builder.master('yarn').config(conf=conf).enableHiveSupport().getOrCreate()
spark.sparkContext.setLogLevel('ERROR')
sc = spark.sparkContext


#step 1: get PySpark DataFrame Ready
msSqlServerdriver = 'com.microsoft.sqlserver.jdbc.SQLServerDriver'
Database_url = 'jdbc:sqlserver://Database_Server;databaseName=your_database_name;user=process_id;password=pword'

accounts_query = '''(select a.account_id, a.column1, b.column2, a.column3
                                from %s a 
                                join %s b on a.account_id = b.account_id 
                               ) query'''%(first_table_nm, second_table_nm)
to_use_df = spark.read.format('jdbc').option('url', Database_url).option('dbtable', accounts_query). \
    option('driver', msSqlServerdriver).load()


#step 2: turn PySpark DataFrame to distributable list
df_groupby = to_use_df.groupby('account_id').agg(func.collect_list('account_id').alias('acc_id'),
                                                      func.collect_list('column1').alias('column1'),
                                                      func.collect_list('column2').alias('column2'),
                                                      func.collect_list('column3').alias('column3'))
df_group = df_groupby.drop('account_id').withColumnRenamed('acc_id', 'account_id')

#step 3: turn PySpark DataFrame to distributable list
def DataScienceModelExtraction(x):
    import subprocess
    import pandas as pd

    ###############--------------------for Model zip folder-----------------################
    def run_cmd(args_list):
        proc = subprocess.Popen(args_list, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        s_output, s_err = proc.communicate()
        s_return = proc.returncode
        return s_return, s_output, s_err

    # Copy Python model files to local executor
    (ret, out, err) = run_cmd(
        ['hdfs', 'dfs', '-copyToLocal', 'your_model_save_location' + '*', '.'])
    ###############--------------------for Model zip folder-----------------################

    import Model.path.main as main
    account_id = x[0]
    column1 = x[1]
    column2 = [int(i) for i in x[2]]
    column3 = [float(i) for i in x[3]]
    dic = {'account_id': account_id, 'column1': column1, 'column2': column2,
           'column3': column3}
    df = pd.DataFrame(dic)

    # Call Python model
    result = main.function_name(account_id[0], df)
    return result
#step 4
rdd = df_group.rdd.map(lambda x: DataScienceModelExtraction(x)).filter(bool)
#step 5
map_rdd = rdd.map(
    lambda i: Row(account_id=int(i[0]), result_column1=str(i[1]), result_column2=str(i[2]), result_column3=float(i[3]),
                  slope_summer=float(i[4]), result_column4=int(i[5])))





```

