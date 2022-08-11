---
tags: Tsql
---
## Query Result side by side. Yes, finally!!

It worked finally. My 12 queries was now 1 query. I only need to run it once to give my result. Yeah!

What did I do to make it work? I made an extra layer. First layer, select the result set as before. Then, select  these result set and add rowID. Thirdly, select second layer result set by joining RowID.


Even though I want to celebrate my creativity/innovativity, I could not and should not. Because after I get my result, I read again the web page that I mentioned in my last post and find out that it uses exactly the same method. I just did not get it right at my first try.

https://stackoverflow.com/questions/13065105/sql-display-two-results-side-by-side 

```tsql


Select A.Name as NameByVisits, B.Name as NameBySpent
    From (Select C.*, RowId as RowNumber From (Select Name From Table1 Order by visits) C) A
    Inner Join
    (Select D.*, RowId as RowNumber From (Select Name From Table2 Order by spent) D) B
    On A.RowNumber = B.RowNumber
    
    
```



Now, you can see the three layers that i mentioned from three layer of select and two inner layers was represented by alias. Because I am not frustrated anymore, I won’t draw any picture with 3D paint to express myself.

 
