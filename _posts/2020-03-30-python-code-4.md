---
tags: Python
---

## When Python Meets SQL–part 5: read/write multiple sheets from one excel file
After I finished re-testing this code, I suddenly realized that SQL connection and SQL commands did not appear. I am happy that this post in only name as part 5. The previous four posts all have SQL inside. This one is just to show how flexible pandas could be. When I had an excel file with several tabs and the data structure on every tab are same, this code was used to save time and to avoid mistakes:


```python
#import
import pandas as pd

#get all sheets into a dataframe map
file_xlsx = r"C:\Users\jingh\python_presentation\file_for_ETL.xlsx"
df_sheet_map=pd.read_excel(file_xlsx, sheet_name=None)

#get data from the above map and form a new dataframe with one additional column 'Sheet_Nms'
dfs = []
for framename in df_sheet_map.keys():
    temp_df=df_sheet_map[framename]
    temp_df['Sheet_Nms'] = framename
    dfs.append(temp_df)

df =pd.concat(dfs) 

#now with the dataframe do anything you need, such us write this df into SQL server, join with an SQL query result dataframe, etc.

#after you have the result dataframe, write it back to excel files with sheets names.
#for demo purpose, I directly copy the dataframe
result_df = df

#create Unique list of sheet names
UniqueNames = result_df.Sheet_Nms.unique()
#create a dataframe dictionary to store your dataframes
DataFrameDict = {elem: pd.DataFrame for elem in UniqueNames}
for key in DataFrameDict.keys():
    DataFrameDict[key]=result_df[:][result_df.Sheet_Nms == key]

#define result_file and write
writer = pd.ExcelWriter(r"C:\Users\jingh\python_presentation\multiple_sheets.xlsx", engine='xlsxwriter')

for tab_name,dframe in DataFrameDict.items():
    dframe.to_excel(writer, sheet_name=tab_name)
    
writer.save()

```


So far, I have finished all I wanted to share about how to use python to quickly ETL data between severs, between excel files and servers. I did not touch simple csv or text file. when I needed to do that, I found the code on this post useful:

https://themichaelskolnik.com/2018/03/12/excel-to-python-to-text/

I had a lot fun using python in my daily work. I hope you will enjoy using it too.
