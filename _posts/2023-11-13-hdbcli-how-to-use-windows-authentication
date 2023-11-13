---
tags: "Data Engineer and Architecture" python
---

## How to connect to SAP HANA with hdbcli and windows authentication  (or windows credential)

When I search online with "python how to connect to SAP HANA", I got to know a super good package hdbcli   (Hana DataBase CLIent, to help memorize its name). It is very easy to use.
However, where I googled how to connect with windows authentication, there is nothing useful. So, I tried several ways and did not work. Finally, I borrowed the pyodbc for SQL server windows authentication " Trusted_Connection='yes'". It worked.

Example code are attached. This code query data from an SAP HANA database and load the result into a SQL server table.

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
