---
tags: Python
---

## When Python Meets SQL –part 2: loop to ETL multiple tables

In part 1 of this series, I mentioned loop after I failed to fast load data to sql server in my test code with my personal computer. In this post, I will show the advantage of using loop in python to move multiple tables/queries. The beauty of learning and using python is we can skip many mouse clicks in SQL server Import and Export Wizard. Let’s begin.

Consider this: we need to move 3 query results from source SQL server to three tables in target SQL server. If using a UI tool such as SQL server Import and Export Wizard, we need to do almost same tedious mouse clicks for three times. With Python, just add an outside loop:


```python
#import

import pyodbc
from datetime import datetime

# three Querie and three target table
query_list = [  """  select * from [schema_test].[table_test20200207a]     """,
              """    select addresses, account from [schema_test].[table_test20200207a]   """,
              """   select account from [schema_test].[table_test20200207a]  """]
target_table_list = ["[schema_test].[table_test20200320b]",
                     "[schema_test].[table_test20200320c] ",
                     "  [schema_test].[table_test20200320d]" ]

#loop through  query to read and  target table to insert
for i in [0,1,2]:
    
    connStr1 = pyodbc.connect('Driver={SQL Server};Server=ServerName;Database=PythonTest;Trusted_Connection=yes;')
    cursor = connStr1.cursor()
    print(query_list[i])
    cursor.execute(query_list[i])
    All_data=cursor.fetchall()
    print(All_data)
    #connect to target SQL Server and load data
    connStr = pyodbc.connect('Driver={SQL Server};Server=ServerName;Database=PythonTest;Trusted_Connection=yes;')
    cursor=connStr.cursor()
    
    #get question marks to for the right number of columns
    number_of_columns = len(All_data[0])
    question_marks = ','.join('?'*number_of_columns) 
    print(question_marks)
    
    
    cursor.fast_executemany = True
    insert_command = """insert into """ + target_table_list[i] 
    insert_command = ' '.join([ insert_command,"""values (""" ,question_marks +""")"""])
    print(insert_command)                                   
    #cursor.executemany(insert_command, All_data)
    connStr.commit()
    cursor.close()
    connStr.close()
```  

I should admit that this time, executemany refused work for me again. I even changed the data type of the source table and make all column varchar. It still did not work. I will study pyodbc executemany and pandas executemany in greater details later to make both works. I will then update on this topic later. On the other hand, I need to think about how to make the code work for row by row insertion. I don’t have a good method yet to produce dynamic parameter list. This is the beautiful thing of learning and sharing–it never ends

For the presentation that I am preparing, I will focus on how to loop and how to play concatenation to form desired sql command in python. The code prints out these lines for my audience.

This paragraph and next code was added on second day after I figure out a way to finally insert data without using executemany. With one more layer of loop, the values will be generated and inserted row by row. This code only apply to situations that all columns are varchar. if other datatype exists, it might throw an error.

'''python
#import

import pyodbc
from datetime import datetime

# three Querie and three target table
query_list = [  """  select * from [schema_test].[table_test20200207a]     """,
              """    select addresses, account from [schema_test].[table_test20200207a]   """,
              """   select account from [schema_test].[table_test20200207a]  """]
target_table_list = ["[schema_test].[table_test20200320b]",
                     "[schema_test].[table_test20200320c] ",
                     "  [schema_test].[table_test20200320d]" ]

#loop through  query to read and  target table to insert
for i in [0,1,2]:
    
    connStr1 = pyodbc.connect('Driver={SQL Server};Server=MyServerName;Database=PythonTest;Trusted_Connection=yes;')
    cursor = connStr1.cursor()
    print(query_list[i])
    cursor.execute(query_list[i])
    All_data=cursor.fetchall()
    print(All_data)
    #connect to target SQL Server and load data
    connStr = pyodbc.connect('Driver={SQL Server};Server=LAPTOP-S1V0PRKL;Database=PythonTest;Trusted_Connection=yes;')
    cursor=connStr.cursor()
    
    for j, row1 in enumerate(All_data):
        print(row1)
        list_of_values = """', '""".join(row1)
        insert_command = """insert into """ + target_table_list[i]
        insert_command = ''.join([ insert_command,""" values ('""" ,list_of_values ,"""')"""])
        print(insert_command)                                   
        cursor.execute(insert_command)
        connStr.commit()
```
