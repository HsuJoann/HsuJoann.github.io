---
tags: Python
---

## When Python Meets SQL–part 1: move data between two SQL servers with fast load


I will organize several code that I have used in my daily work to do simple ETLs. This post will be first one: how to move table (or query result from one SQL server) to another. Even though the task could be handled pretty easily in SSIS and SS Import and Export Wizard, the python code provides the perfect starting point for SQL developers to use python.


```python
#import

import pyodbc
from datetime import datetime


#connect to Source SQL server and get data

#connStr1 = pyodbc.connect('Driver={SQL Server};'
#                      'Server=myservername;'
#                      'Database=PythonTest;'
#                      'Trusted_Connection=yes;')

connStr1 = pyodbc.connect('Driver={SQL Server};Server=LAPTOP-S1V0PRKL;Database=PythonTest;Trusted_Connection=yes;')
cursor = connStr1.cursor()
SQL_command = """ select top 3
* 
 FROM [pythontest].[schema_test].[table_test20200207]

"""
# note for this variable SQL_command: use """ """ so that ' ' could be used in the sql code
#pyodbc package can take care a lot of sql command like declare variables, while pyspark package won't do that
#will discuss how to parameterize SQL_command with placeholder later in other post

cursor.execute(SQL_command)
All_data=cursor.fetchall()
# 
print(All_data)

#two variable for performance monitoring
number_of_records = len(All_data)
start_time = datetime.now()

#connect to target SQL Server and load data
connStr = pyodbc.connect('Driver={SQL Server};Server=myservername;Database=PythonTest;Trusted_Connection=yes;')
cursor=connStr.cursor()
cursor.fast_executemany = True
cursor.executemany(""" insert into [pythontest].[schema_test].[table_test20200207a]
                         values (?,?,?)""", All_data)
connStr.commit()
cursor.close()
connStr.close()

#show the result of performace monitoring
end_time = datetime.now()
print("done. Loaded ", number_of_records, " records from ", start_time, " to ", endtime)
I have used fast load for many times successfully in my work computer. However, I have no idea why the code stop working today when I prepare for a presentation with my own personal computer. The reason I got is memory error. I tried some remedies provided by online forums and it did not work. It is OK. I will show loading row by row with these following code in my presentation:

connStr = pyodbc.connect('Driver={SQL Server};Server=myservername;Database=PythonTest;Trusted_Connection=yes;')
cursor=connStr.cursor()
for i, row1 in enumerate(All_data):
    cursor.execute(""" insert into [pythontest].[schema_test].[table_test20200207a]
                         values (?,?,?)""",row1[0], row1[1], row1[2])
    connStr.commit()
 
 
 ```
The presentation that I am preparing has same title as this post: When Python Meets SQL. It is not a coincidence. Ever since I started using python and learn more and more about how python can be used in everyday work of a SQL developer, I have the passion to tell everyone about all the details, which you will see in the many posts following this one.
