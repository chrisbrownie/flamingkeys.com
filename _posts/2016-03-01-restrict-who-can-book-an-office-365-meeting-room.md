---
layout: post
title: Restrict who can Book an Office 365 Meeting Room
date: 2016-03-01 00:36:02.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Exchange
- Microsoft
- Office 365
- PowerShell
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '4623220631'
  dsq_needs_sync: '1'
  amazonS3_cache: a:2:{s:121:"//flamingkeys.com/wp-content/uploads/2016/03/2016-03-01-11_30_13-Mail-Chris-Brown-Outlook-Internet-Explorer-InPrivate.png";i:1116;s:129:"//flamingkeys.com/wp-content/uploads/2016/03/2016-03-01-11_30_13-Mail-Chris-Brown-Outlook-Internet-Explorer-InPrivate-300x221.png";i:1116;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/03/restrict-who-can-book-an-office-365-meeting-room/
---
Restricting the ability to book a meeting room is a popular scenario for organisations with boardrooms and other such meeting rooms that cannot be booked by the majority of employees. Typically this is addressed by the use of [mailbox delegates](http://exchangeserverpro.com/exchange-server-2013-room-equipment-mailboxes/), though for some this is cumbersome where a significant group of users might require the ability to book a resource, but not all of them should be notified to approve requests.

Exchange Server permits this through the use of In Policy and Out of Policy requests. Put simply, an in-policy request is a "healthy" request that doesn't meet a few conditions such as working hours, meeting length, meeting overlap, etc. Out of Policy requests are anything that match the aforementioned conditions. Microsoft say it better than I could possibly articulate on [TechNet](https://technet.microsoft.com/en-us/library/bb124542(v=exchg.141).aspx).

Using PowerShell, it's simple to turn off the ability for the majority of users to book a resource, while allowing only a select group of users to make in-policy bookings (note that any delegate behaviours will still take effect for out-of policy requests made by these users):

First, connect [PowerShell to Exchange Online](https://technet.microsoft.com/en-au/library/jj984289(v=exchg.160).aspx). Run the following commands to configure the room "BoardRoom" to accept bookings only from Executives, Executive Assistants, and Sam L, the facilities maintenance manager who occasionally books the room for cleaning and to change lightbulbs.

{% highlight powershell %}
Get-Mailbox BoardRoom | Set-CalendarProcessing -AllBookInPolicy:$false -AllRequestInPolicy:$false -BookInPolicy "Executives@contoso.com","EAs@contoso.com","Sam.L@contoso.com"
{% endhighlight %}

Easy as that. Anyone attempting to book the room while not a member of the above groups will receive a rejection message:

![]({{ site.baseurl }}/assets/2016-03-01-11_30_13-Mail-Chris-Brown-Outlook-Internet-Explorer-InPrivate.png)
