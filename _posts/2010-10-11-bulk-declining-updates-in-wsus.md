---
layout: post
title: Bulk Declining Updates in WSUS
date: 2010-10-11 17:53:15.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Computers
- SQL
- tips
- windows
- WSUS
redirect_from:
  - /2010/10/bulk-declining-updates-in-wsus/
---

Windows Server Update Services (WSUS) is a fickle beast, at best. Anyone who has ever used it on a large (or, for that matter, small or medium) network knows just how much TLC it requires in order to function even semi-happily. I have recently been tasked with deploying it to our network of over 1500 laptops and 100 or so desktops, all of which currently update direct from Microsoft Update on the internet. There is a WSUS server in place, but it was set up quickly and hasn't been maintained since installation. It was receiving updates for nearly all Microsoft products, and updates had never been approved or even looked at, so I had quite a hefty job ahead of me, deleting all the irrelevant updates. Being the enthusiastic (read: lazy) systems administrator that I am, the first thing I did was hunted for a script to do this for me...Needless to say, I couldn't find anything. I managed to remove the unwanted application updates using the _Options -> Products and Classifications_ section, however WSUS provides no options for removing architecture-specific updates. We don't have any Itanium servers here, so I don't need the updates.

*Update #1:* If you're running WSUS on a version of Windows Server that has the UpdateServices module (At least Windows Server 2012 and beyond), you can do this with PowerShell. See the bottom of this post for directions.

*Update #2:* As mentioned in [a TechNet Thread](http://social.technet.microsoft.com/Forums/en-US/winserverwsus/thread/4ef94f94-0521-473e-89fe-efac4a1b499a/), executing T-SQL directly against your SUSDB is not supported by Microsoft. The WSUS database schema is liable to change at any point. Please keep in mind that neither Microsoft nor I can be held responsible should you fry your database.

A quick query of the database showed I had 500-odd of these:

```sql
SELECT COUNT([UpdateId]) FROM [SUSDB].[PUBLIC_VIEWS].[vUpdate] WHERE DefaultTitle LIKE '%Itanium%' AND IsDeclined=0
```

To bulk decline these updates en mass the following script had to be created:

```sql
DECLARE @RC int;
DECLARE @count int;
DECLARE @updateID uniqueidentifier;
DECLARE @adminName nvarchar(385);
DECLARE @failIfReplica bit;
DECLARE @tbl table(ID int identity(1,1) primary key not null, updateid uniqueidentifier not null)

INSERT @tbl(updateid) (SELECT [UpdateId]
  FROM [SUSDB].[PUBLIC_VIEWS].[vUpdate]
  WHERE ( DefaultTitle LIKE '%Itanium%' OR DefaultTitle LIKE '%ia64' ) AND ( DefaultTitle NOT LIKE 'x86' OR DefaultTitle NOT LIKE 'x64' )
  And IsDeclined=0)

SET @count = 1;
SET @adminName='SQLDecline'
SET @failIfReplica=0

WHILE @count &amp;lt;= (SELECT COUNT(*) FROM @tbl) BEGIN
                SET @updateID = (SELECT updateid FROM @tbl WHERE ID = @count);
                EXECUTE @RC = [SUSDB].[dbo].[spDeclineUpdate] @updateID, @adminName, @failIfReplica
                SET @count += 1;
END
```

This allowed me to decline every Itanium update in WSUS, significantly reducing the number of updates for me to sift through manually. As it leverages the built in stored procedure 'spDeclineUpdate' in the WSUS database you can fairly safely assume it will decline the updates appropriately.

## The PowerShell Way

If you're running Windows Server 2012 or newer, you can do this really easily with PowerShell:

```powershell
Get-WsusUpdate | 
  Where {$_.update.title -ilike "*itanium*" -or $_.update.title -ilike "*ia64*"} | 
  Deny-WsusUpdate
```

Warning: this is very, very, very slow. But it will get there eventually. Probably.

I hope this helps you out and makes this service slightly easier to manage.
