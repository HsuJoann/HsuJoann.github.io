---
tags: Tsql
---


## Install a SQL Server Virtual Machine in the Cloud


At the end of my last post, I mentioned a faster way to have a SQL Server running. That way is: to use SQL Server in the Cloud. The Pros of getting a SQL server virtual machine (VM) in the Cloud are:


time saved on installing one on your own computer.
no resource used on your own computer. This is very important if your computer is old and running slow.
experience of using cloud services. Since Cloud Computation is a such big buzzword, using cloud services makes me feel good.
your will have a backup in the Cloud. Consider this scenario, you are going to give a talk  with demo of SQL server, but your computer just does not work. Now, with a VM in the cloud, you can continue your talk on any computer with internet connection and a web browser.
The Cons are:  Running a computer in the cloud cost money. We can reduce our cost to zero by two ways. First, claim the Microsoft $25 per month credit. Second, shut down your virtual computer, stop and deallocate the resource each time after your play. Every time you use it, you need to  login on Azure, to start your VM, and to stop after use. That is about 6 minute each time. But, that will help our cost factor under control.

With the benefits and disadvantage in your mind, you are ready to install one.



Remember this page? Where you started to get SQL Server developer edition. Among many other benefits, Microsoft give you $25 Azure credit every month so that you can learn and have fun with Azure.  Click on the Azure benefit and eventually, you will come to the Azure portal. Follow this video to get a new VM with SQL server 2016 Enterprise edition.

https://youtu.be/L6RJHxQ52Hs


As I mentioned in the video, I will show you how to install a sample database. We are on our way to have a lot of fun.




STOP!!! This way of playing SQL Server Virtual Machine is too expensive. The default setting used more than 1 TB blob storage and costed me more than $150 per month. Even when I shut down the virtual machine completely, the blob storage is eating my money 24/7. So, stop here, don’t use virtual machine as I posted in the post initially (July 2017).

I will do more research and find a way to use less than $25 per month (spending limit of developer).  (shutdown the machine completely will stop charging. added by Joann on 2022-08-10)

I will do another post soon to reveal a way to use virtual machine completely free (of course with other limitations). Stay tune.  (this line is not true anymore. this method did not exist anymore. --added by Joann on 2022-08-10)
