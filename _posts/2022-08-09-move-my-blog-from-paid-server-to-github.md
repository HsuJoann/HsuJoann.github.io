---
tags: YEAH!
---

## Move My Old Blog from Paid WordPress to GitHub

To achieve what I described in the title of this blog, I follow this fabulous blog by Chad Baldwin   (https://chadbaldwin.net/2021/03/14/how-to-build-a-sql-blog.html). I am super proud of this fellow TSQLer.

For the past several years, I spent about $100 per year to keep my old blog running on Godaddy platform. 
Since my blog only viewed by a few people, it does not make any sense to keep it that way. GitHub is adopted wildly by tech community and it is a better platform. Plus, it is free. So, here I come.

 ```tsql
 SELECT Max(value/cost)
 FROM sys.blog_platform
 
 --Gitbub
 ```


(In case someone is wondering about a best coder way, like export/import. I did not do that. I have about 30 or so posts in my old blog. so, I manually copy/paste everthing. The most none high tech way 😊.  )

Q1: How to setup the whole blog site? 

Answer1:          above mentioned article of Chad Baldwin.

Q2: How to add tags (so that the Blog Archive could have several categories? 

Answer2:          above mentioned article of Chad Baldwin, in the comment section. briefly speaking: add the following to the beginning of the post

'''

---

tags: Tag_name

---

'''






Q3: how to add pictures 

Answer3: above mentioned article of Chad Baldwin, in the comment section. (but I used google result)

Q4: how to add links to down load files 

Answer4: https://stackoverflow.com/questions/18062553/how-can-i-add-a-downloadable-file-to-my-github-io-page

Q5: how to add pages

Answer5: have not tried yet. looks like in the comment session of above page


Q6 & Answer6: for tons of other things. here is the link I found:
https://about.gitlab.com/handbook/markdown-guide/

