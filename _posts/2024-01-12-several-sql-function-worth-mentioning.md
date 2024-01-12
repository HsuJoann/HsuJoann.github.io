---
tags: Tsql
---

## Several SQL function that saved me a lot of time

Even though I have shared a lot of cool stuff in Python and PySpark, SQL remains the heart of my data manipulation. 
Yesterday, I used two SQL functions  TRANSLATE  (more details   https://learn.microsoft.com/en-us/sql/t-sql/functions/translate-transact-sql?view=sql-server-ver16    ) and REVERSE (more details    https://learn.microsoft.com/en-us/sql/t-sql/functions/reverse-transact-sql?view=sql-server-ver16         ). These two functions saved me a lot of time to encrypte account_id because the old code used looping and forfeited the strength of SQL. I searched and found that these two functions are not unique to Tsql, PostgreSQL also has these two convinient functions.


The experience of yesterday make me finally writing two new Tsql functions that I had wanted to write about for a long time. 
https://stackoverflow.com/questions/194852/how-to-concatenate-text-from-multiple-rows-into-a-single-text-string-in-sql-serv   has almost all that I want to discuss. Before SQL Server 2017, the only way to use is lengthy code 


```python

from hdbcli import dbapi
conn = dbapi.connect(
    address="yourserver", 
    port=portnumber, 
    Trusted_Connection='yes'
)

cursor = conn.cursor()
cursor.execute("""       select column1, column2, column3
   from schema.table_name
 where  "the_column_Timestamp"> TO_TIMESTAMP('2023-10-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS')

 """)

All_data_1 = cursor.fetchall()
All_data = [list(row) for row in All_data_1]



import pyodbc
from datetime import datetime

number_of_records=len(All_data)
start_time = datetime.now()

connStr = pyodbc.connect(r'Driver={SQL Server};Server=servername,portnumber;Database=databasename;Trusted_Connection=yes;')
     
cursor = connStr.cursor()
cursor.fast_executemany = True
cursor.executemany("""INSERT INTO targettable_schema_and_name_here values(?,?,?)
                          """, All_data )
connStr.commit()
cursor.close()
connStr.close()
end_time = datetime.now()

end_time = datetime.now()

#showing off fast load speed
print('done: loading of ', number_of_records, ' records from :', start_time, '   to    ', end_time)


```
