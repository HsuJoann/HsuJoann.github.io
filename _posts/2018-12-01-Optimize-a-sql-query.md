---
tags: Tsql
---

## Trick SQL Server to use the best Execution Plan


What is the two most important words that one should look for in SQL Server Execution plan?  Scan ! Seek!

SCAN a big table is a disaster. The rescue is: try your best to let SQL  Server SEEK a big table.

(William Wolf taught me this many many years ago at a SQL Saturday meeting. This knowledge is so powerful and it makes one of my query work so good that I want to share with you.)


Last week, I worked on a query that joins and aggregates two of the biggest tables in our system: charge and weather. Each of these table contains “billions of rows” (quote what my supervisor says). When I tried to write the query, I only queried with one test account number (where account_ID = ******). It took about 2 second to return the result set back to me. I accepted it because I knew in the back end the two monster table were working hard. After everything looked good, I commented out that where clause and waiting for the result set (for about 3000 account numbers). I waited and waited, waited, and waited… … two hour has passed and no result. Why? Why? Why? I yelled inside.

The answer was easy: I took SEEK for granted and did not check my execution plan. When the clause where account_ID = ******     was there, SQL Server SEEKed through my huge tables. Even though I knew that I only need 3000 account in my real query, SQL did not know that it started SCAN mode.

I tried several ways to help SQL Server to repent and turn to SEEK. First, I updated index and statistics of the small table which contains 3000 account numbers. I check execution plan: Why still ‘SCAN’? I don’t know the answer. But, then I tried add where clause: where account_ID in (Select account_ID from the_small_table). Well, still SCAN? finally, I had to play my dirty trick: I copied those three accounts numbers and wrote where clause like this:

where account_ID in

(….. ,

……. ,

…….  ,

……. ,

.

.

.

.

……. )

 

 

The query now is very long with more than 3000 rows but SQL server finally changed the plan from SCAN to SEEK.

3 minute, viola. I got the result data set. Such a good experience. At Willian Wolf’s speak in SQL Saturday, he led the audience sing a short song “SEEK n’ DESTROY’. The moment I saw the result data set, I began to sing that song.

 
