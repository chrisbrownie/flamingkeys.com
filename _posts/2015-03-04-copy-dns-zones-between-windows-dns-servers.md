---
layout: post
title: Copy DNS Zones between Windows DNS Servers
date: 2015-03-04 23:30:17.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- DNS
- Microsoft
- PowerShell
- Scripting
- Server 2003
- Server 2008
- Server 2008 R2
- Server 2012 R2
- Server Manager
- tips
- windows
meta:
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3568164214'
  amazonS3_cache: a:3:{s:72:"//flamingkeys.com/wp-content/uploads/2015/03/2015-03-05-10_27_01-DNS.png";i:915;s:79:"//flamingkeys.com/wp-content/uploads/2015/03/2015-03-05-10_27_01-DNS-300x10.png";i:915;s:79:"//flamingkeys.com/wp-content/uploads/2015/03/2015-03-05-10_27_01-DNS-730x24.png";i:915;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Consider a scenario where you're migrating Secondary DNS zones between multiple Windows DNS servers. This is a tedious process at best when completed via the GUI. Fortunately, PowerShell makes this considerably easier. With just one command, all Secondary zones can be copied across from Server A to Server B. This even works with old versions of Windows, provided you run the commands remotely from Windows Server 2012 R2's DnsServer module:

![]({{ site.baseurl }}/assets/2015-03-05-10_27_01-DNS.png)

Try it for yourself!

{% highlight powershell %} 
Get-DnsServerZone -ComputerName DNS1 | Where {$_.ZoneType -eq "Secondary"} | Add-DnsServerSecondaryZone -ComputerName DNS2
{% endhighlight %}
