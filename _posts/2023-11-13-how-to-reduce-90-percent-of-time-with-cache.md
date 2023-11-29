---
tags: pySpark
---

## How to use cache and broadcast to increase the code effeciency dramatically

When I read some claims saying that using cache() can reduce time by 90%, I doubt the validity. But, last week, I experienced it myself. it was very exciting and I think it is worth sharing. 
The code I worked on was like this: a pyspark dataframe with roughly 0.4 million rows. The data cleansing took about 10 minutes of running time. Then, the subsets of this dataframe were wenting through their own model to get three new columns. At last, the result datasets was combined and saved in sql server. We all had thought the going-through-model part was time consuming, with each took longer than 10 minutes. But, I used cache and it turned out that the code was slow not because going-through-model part, but because its data cleansing was repeated for each subset.
Now, I have a solid grisp about how cache(). and I can use cache and broadcast effectively. 




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
