---
tags: Python
---

## When Python Meets SQL-part 3: load excel file data into SQL Server without creating table

At the beginning of my Python ETL journey, I created tables in SQL server and insert to those tables. Back then, I thought this is the only way. During my work using pySpark, I used pySpark to write SQL tables from pySpark dataframe. The best thing is I don’t need to create tables pySpark does all for me. (I will wrote pySpark codes later). That good experience makes me think: is there a better way in python? Is there a way to write data into SQL server without creating table?

My best friend google told me: yes. And here is how:

```python

#imports
import pandas as pd
from sqlalchemy import create_engine
import pyodbc

#read excel file content into a pandas datafram
file_xlsx = r"C:\Users\jingh\python_presentation\file_for_ETL.xlsx"
df=pd.read_excel(file_xlsx)
df


#make connection to SQL server and write the dataframe content to a table 
myservername = 'LAPTOP-S1V0PRKL'
mydatabasename = 'pythontest'

sqlcon = create_engine('mssql+pyodbc://@' + myservername + '/' + mydatabasename + '?trusted_connection=yes&driver=ODBC+Driver+13+for+SQL+Server')

df.to_sql('ETLtest20200321', con = sqlcon, schema ='schema_test', if_exists = 'replace', index = False)

#note on my personal computer ODBC driver is 17, but my company computer is 13 
#because my personal computer has SQL Server 2019 but company is SQL Server 2016
#check your computer ODBC version and put it in the create_engine string

```

Yes. Simple as That.

But, there is a shortcoming for this method: slow. It takes about 100 minutes when I wrote 1 million rows. The slowness is because of row-by-row operation between python and SQL server. I have tried many times to get it done faster (many people have tried many times too). So far, I can only fast load with string datatype. In short, it is done this way: First convert the dataframe to a list of tuples. Then, use ODBC connection and execute many to fast load.

```python


list_of_tuples=list(df.itertuples(index=False, name=None))

SQL_insert_command = """INSERT INTO [schema_name].[table_name](	[Column1]       ,
	[Column2]   ,
	[Column3]    ,
	[Column4],
	[Column5]   ,
	[Column6],
	[Column7] )
                               values (?,?,?,
                                       ?,?,?,?)"""


connStr = pyodbc.connect(r'Driver={SQL Server};Server=myservername;Database=mydatabasename;Trusted_Connection=yes;')


cursor = connStr.cursor()

cursor.fast_executemany = True
cursor.executemany(SQL_insert_command, list_of_tuples )

connStr.commit()
cursor.close()
```
I am still trying for broader datatypes. Of course I will share my progress here after I have a break through.

You can see from this code that a new package, pandas is used here. In pandas, a table is called dataframe. All of our knowledge about sql server tables can be applied to dataframe, just with different code.
