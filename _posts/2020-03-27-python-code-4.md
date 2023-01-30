---
tags: Python
---

## When Python Meets SQL — part 4: writing multiple excel files into SQL server through loop


This post is closely related to part 3, with one out loop. Sometimes, we need to load several files into SQL server. When these several files are all in this folder, the follow code comes really handy:


```python
#imports
import os
import pandas as pd
from sqlalchemy import create_engine
import pyodbc

#created directory
os.chdir(r"C:\Users\jingh\python_presentation\several_excel")
#create a list for the excel files in the folder
path = os.getcwd()
files = os.listdir(path)
files_xlsx = [f for f in files if f[-4:]=="xlsx"]

print(files_xlsx)

#connection to SQL server
server = 'MYserverNAME'
database = 'pythontest'
sqlcon = create_engine('mssql+pyodbc://@MYServerName/pythontest?trusted_connection=yes&driver=ODBC+Driver+17+for+SQL+Server')

# loop through files to load them in pandas dataframe then to SQL server 
# each table name has part from the file name
for f in files_xlsx:
    df = pd.read_excel(f)
    df.to_sql('test_'+f[:-5], con = sqlcon, schema ='schema_test', if_exists = 'replace', index = False)

#for large files, loading speed is about 10k rows/minute
print('done')
```
