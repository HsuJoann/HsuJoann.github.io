---
tags: Tsql
---

## query result side by side? NO!
I have twelve queries. Each of the query select 20 random account numbers that fits its own special where clauses. For twelve times, I need to copy the SQL code in TOAD, run the query in DB2, copy paste the 20 lines of result to one final excel file, which contains twelve columns.

Of course, I want to change it. I think it should not be too difficult.

![Book logo](/least-github-pages/assets/blogpost2018-04-21-768x356.jpg)

First Attempt: UNION ALL

I think each query, instead of one column, I select two columns, one with my desired result, another is a number to indicate which set the account belongs to. I will Union them all, to get one result set like this:

Account_number      Set_belong_to

Account1234564      1

Account1239662      1

Account589373        1

Account08739           1

…

Account0983744      1

Account3434564     2

Account9039662     2

Account4879373        2

Account497399          2

…

Account439744      2

…

After successfully get this list, I will try to pivot the list according to Set_Belong_to.

What I get is a mystery. I tried twice. First time, my TOAD died without giving me any result. Second time, no result after 20 minutes. I got scared. The reason was I could only try my ideas on an OLTP production platform. Of course I don’t want to shut down the production database because of my ideas. Maybe UNION ALL is not good. I have no clue but I don’t even dare to go further.

Second Attempt: ROW_NUMBER() OVER()

I googled and get inspired by these two results

'''tsql
    Select A.Name as NameByVisits, B.Name as NameBySpent
From (Select C.*, RowId as RowNumber From (Select Name From Table1 Order by visits) C) A
    Inner Join
    (Select D.*, RowId as RowNumber From (Select Name From Table2 Order by spent) D) B
    On A.RowNumber = B.RowNumber
--https://stackoverflow.com/questions/13065105/sql-display-two-results-side-by-side

SELECT
    books."id",
    TRIM(books."title") title,
    ROW_NUMBER() OVER () rownumber
FROM
    books;
--https://chartio.com/resources/tutorials/how-to-use-row_number-in-db2/
'''
I tried. But the first set gave rownumber for 1 to 20 as expected. The second set gave random numbers that are not even  sequential. I need to figure out why. Then, I must figure out Full Outer join because some of my sets are no 20 line.

Third Attempt: Cross Join

Again, google search helped me to get this idea

```tsql
Select List1.Result1, List2.Result2
 
from
 
(Select...From...Where) as Result1, (Select...From...Where) as Result2
```
This code is so simple, elegant and attractive, until I tried.

Yes, I got two column that I wanted. BUT, I got 400 rows instead of 20. Because this code cross join my result set. Well, I can hand pick them by adding row number and show only these rows (1, 22, 43, …, 20*i +i+1, 400)

like this

 
'''sql

Select * from
 
(Select List1.Result1, List2.Result2, ROW_NUMBER() OVER () as line_to_pick
 
from
 
(Select...From...Where) as Result1, (Select...From...Where) as Result2) as Crossed_result
 
Where line_to_pick in (1, 22, 43,...400)


'''


Guess what? It works. Before I got too self-satisfied, I tried three sets cross join. Now, I need to hand pick 20 out of 8,000 lines. Like this where line_to_pick in (1, 422, 843,…,8000). The secret formula is 420*i+i+1 (i=0, 1, 2, 3,…,19). I can not go on anymore. The number will be outrageous.

Most importantly, it won’t help my situation because of two reasons: 1.  some of my set might only return 5 today and 12 tomorrow. I can not figure out a dynamic way to pick the line. 2. In theory, relational query result is return without guarantee order. This way only stands on pure luck.

What can I do now? Well, I will continue think about it. But for now, let me draw something to express my frustration. I will post my drawing as the picture for this post 🙂

 
this post looked wierd because (I guess) some sql code is not Tsql but DB2 sql. Since I worked with several flavor. this is all I got. not correct it and leave as it is. --Joann added note on 2022-08-10.
 
