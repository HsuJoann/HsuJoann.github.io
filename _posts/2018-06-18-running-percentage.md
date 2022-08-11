---
tags: Tsql
---

## Running Total Percentage
Window functions of SQl server are very useful. Many webpage like this one https://www.brentozar.com/sql-syntax-examples/window-function-examples-sql-server/  have written how to use them.  I want to share with you one segment of my sql code to get running total percentage using window functions.


I have this scenario and problem to solve: Every night, hundreds (or thousands) of our data warehouse table are updated. We want to know the updated time of those table that we are interested. So, I want to have the updated count and running total percentage for the update.

When I search for running total percentage in google, this two appears on the top of the list.   https://www.1keydata.com/sql/sql-cumulative-percent-to-total.html       https://stackoverflow.com/questions/12931118/calculate-running-percentage-in-sql

However, it seems that the total of these two examples are only one number.  In my example, I used windows function for both the total and the single row to get running total for different windows.

here is my code:

```tsql

select Schema_nm, table_Name, Load_time, number_updt, hour_of_day
 
,sum(NUMBER_UPDT) OVER (PARTITION BY Schema_nm, table_Name ORDER BY Load_time) as Running_Total
 
,CAST(CAST(sum(NUMBER_UPDT) OVER (PARTITION BY Schema_nm, table_Name ORDER BY Load_time) as decimal(18,2))/CAST(sum(NUMBER_UPDT) OVER (PARTITION BY Schema_nm, table_Name) as decimal(18,2))*100 as int) as running_percentage
 
from
 
(
 
select Schema_nm, table_Name, case when HOUR_OF_DAY >17 Then HOUR_OF_DAY when HOUR_OF_DAY <=17 Then HOUR_OF_DAY+24 end as Load_time, number_updt, hour_of_day
 
from
 
(
 
SELECT 'Schema_1' as Schema_nm, 'table_1’ as table_Name,  DATEPART(HOUR, Last_Updated_Date) AS HOUR_OF_DAY, COUNT (Table1_id) AS NUMBER_UPDT FROM Schema_1.table_1 WHERE Last_Updated_Date>GETDATE()-90 GROUP BY DATEPART(HOUR, Last_Updated_Date) UNION All
 
SELECT  'sechema_2' as Schema_nm,'table_2' as table_Name,  DATEPART(HOUR, Last_Updated_Ts) AS HOUR_OF_DAY, COUNT (table2_id) AS NUMBER_UPDT FROM schema_2.table_2 WHERE Last_Updated_Ts>GETDATE()-90 GROUP BY DATEPART(HOUR,Last_Updated_Ts )
 
) as result_set
 
) as result_set1
 
Order by Schema_nm, table_Name, Load_time

```

The part:

when HOUR_OF_DAY >17 Then HOUR_OF_DAY when HOUR_OF_DAY <=17 Then HOUR_OF_DAY+24 end

is for: We want to see the update time starting from 18:00 when schedules job starts. Not from 0:00, although this is the natural number to start mathematically.

