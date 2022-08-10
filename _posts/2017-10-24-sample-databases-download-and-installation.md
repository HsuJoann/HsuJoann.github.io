---
tags: Tsql
---


## Sample Databases Download and Installation


After your installation of SQL Server, it is time to get some sample database.

The most common sample database is Adventure Works and Adventure Works DW  (AdventureWorks Data Warehouse) provided by Microsoft. Majority of books and videos use these sample databases.
(  https://www.microsoft.com/en-us/download/details.aspx?id=49502&751be11f-ede8-5a0c-058c-2ee190a24fa6=True   )

In 2016, together with the release of SQL Server 2016, Microsoft has provided a new sample databases called Wide World Importers and Wild World Importer DW. Newer videos by Microsoft are using these sample databases.
( https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0  )



Here is the link for all these four. The downloaded files end with.bak, standing for backup file. So, the way to get them into SQL Server is to “restore” it. The most common ways includes “restore” by GUI (graphic user interface) and to restore by SQl script.

First of all, save the whatever.bak on C:\data folder.

In SSMS, right Click Database in Object Explorer, select Restore Database from the dropdown manual  (as shown in this picture)    . After click Restore Database, In the pop-up window, choose Device button (under source catagory), and then click the … button at the end of Device line. In the pop-up “Select backup devices” window, click Add. Then, in the “Locate backup file window, choose the file that you want to restore (as shown in this picture). . Finally, click OK and OK. In a little while, your database is ready to use.

Another sample database is Movies provided by Wise Owl. You can download it at their website. I will discuss more about Wise Owl later. Back to database Movies, after it is extracted, it is a file with.sql as extension. You can double click it to open it in SQL Server Management Studio (SSMS), there, you could read the SQL langurage, like CREATE DATABASE, CREATE TABLE, INSERT, GO…… Execute this script by click on the Execute button, you will have it installed.
(https://www.wiseowl.co.uk/videos/sql2016/movies-database.htm)

I know a sample database that is not well known. It is provided with the book, Pro SQL Server2012 Reporting Services, by Brian McDonald, Shawn McGehee, Rodney Landrum. The download link is on the Apress website. This is a health-care database, with tables aboout patient, diagnose, charge, etc. Of course, this database is mostly used by readers of this book. (The book is available through ITPro service, Greenville county library patron has free access to ITPro)
(http://www.apress.com/us/book/9781430238102)
(http://www.apress.com/us/book/9781430238102)
 

If these several sample still don’t satisfy your big appetite, try to google for more.
