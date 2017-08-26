---
layout: post
title: How DNS Aging and Scavenging Actually Work
date: 2015-01-23 02:52:56.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- Computers
- DNS
- Microsoft
- Server 2003
- Server 2008
- Server 2008 R2
- Server 2012
- Server 2012 R2
- windows
meta:
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3447124140'
  amazonS3_cache: a:9:{s:59:"//flamingkeys.com/wp-content/uploads/2015/01/zoneconfig.png";i:878;s:67:"//flamingkeys.com/wp-content/uploads/2015/01/zoneconfig-150x150.png";i:878;s:61:"//flamingkeys.com/wp-content/uploads/2015/01/serverconfig.png";i:877;s:57:"//flamingkeys.com/wp-content/uploads/2015/01/timeline.png";i:889;s:67:"//flamingkeys.com/wp-content/uploads/2015/01/zoneconfig-144x144.png";i:878;s:68:"//flamingkeys.com/wp-content/uploads/2015/01/serverconfig-300x70.png";i:877;s:64:"//flamingkeys.com/wp-content/uploads/2015/01/timeline-300x34.png";i:889;s:64:"//flamingkeys.com/wp-content/uploads/2015/01/timeline-730x82.png";i:889;s:65:"//flamingkeys.com/wp-content/uploads/2015/01/timeline-900x101.png";i:889;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
excerpt: There's a lot of conversation on the 'net about how DNS Aging and Scavenging
  within Windows Server work, but lots of it doesn't make sense, and most of it is
  pretty convoluted. I'll aim to clear that up below in plain English.
---
There's a lot of conversation on the Internet about how DNS Aging and Scavenging within Windows Server work, but lots of it doesn't make sense, and most of it is pretty convoluted. I'll aim to clear that up below in plain English.
Consider an environment with a bunch of Domain Controllers all holding the DNS server role, and a bunch of clients. Sound familiar? I hope so. Let's assume you've followed best practice and have DNS aging/scavenging configured on all zones, and the scavenging process configured on a single DNS server (use <a href="http://blogs.msdn.com/b/muaddib/archive/2013/08/03/how-to-determine-which-dns-server-s-have-scavenging-enabled.aspx" target="_blank">this script </a>to make sure, thanks <a href="https://twitter.com/nickstugr" target="_blank">@nickstugr</a>). Our server is called MELDC01, and our client is called COMPUTER1.
This is the configuration of the DNS zone: <a href="https://flamingkeys.com/wp-content/uploads/2015/01/zoneconfig.png"><img class="aligncenter size-thumbnail wp-image-878" src="{{ site.baseurl }}/assets/zoneconfig-150x150.png" alt="DNS Aging Scavenging Zone Config" width="150" height="150" /></a>
...and this is the configuration on our server MELDC01: <a href="https://flamingkeys.com/wp-content/uploads/2015/01/serverconfig.png"><img class="aligncenter size-full wp-image-877" src="{{ site.baseurl }}/assets/serverconfig.png" alt="serverconfig" width="380" height="89" /></a>
Keeping the configuration settings above in mind, let's dig a little into how the "conversation" goes between client and server. Client can be a PC, a server, or any other device that registers itself in DNS.
First we enter the **no-refresh interval**. In this aptly-named stage, the record cannot be updated by the client.
<table>
<tbody>
<tr>
<td>**T+0**
**Client:** Hey, MELDC01! I'm COMPUTER1 and my IP address is 10.0.0.123
**Server:** Hey, COMPUTER1! Welcome aboard, I don't see a record for you, so I'll create one and stamp it with the current time</td>
</tr>
</tbody>
</table>
In the no-refresh interval of T+0 through T+7 days (or the configured no-refresh interval), the following occurs:
<table>
<tbody>
<tr>
<td>**T+0 through T+7 days**
**Client:** Hey, MELDC01! I'm COMPUTER1 and my IP address is 10.0.0.123
**Server:** Hey, I don't care, go away please</td>
</tr>
</tbody>
</table>
During the no-refresh interval, the record cannot be automatically updated. Once the no-refresh interval expires, we enter the refresh interval:
<table>
<tbody>
<tr>
<td>**T+7 through T+14 days**
**Client:** Hey, MELDC1! I'm COMPUTER1 and my IP address is 10.0.0.123
**Server:** G'day, COMPUTER1! I have a record for you here, but it's over 7 days old. Let me update that and restamp it with the current time. You now have another 7 days of no-refresh time to enjoy</td>
</tr>
</tbody>
</table>
At this point, we return to T+0 - the record is effectively brand new and we're back in the no-refresh interval.
If, however, the client does not communicate with the server during the refresh interval, we enter the scavenging period (T+refresh+no-refresh, or T+7+7 days). This is the point at which, when it runs, the server's scavenging process will come along and delete the record. One common misconception is that this is an immediate action, however it's anything but. That's where the "Scavenging period" configured on the server comes into play. This could perhaps, more aptly, be called the scavenging interval. This is how frequently the scavenging process runs on the designated DNS server.
When the scavenging period is configured on a DNS server, the timer starts for 7 days (or whatever the configured value is). Once the timer expires, the scavenging process runs. It checks every zone on the server with Aging/Scavenging configured, and deletes any record that hasn't been refreshed in T+refresh+no-refresh days. After the scavenging process runs, the timer again begins to count down the appropriate number of days.
From this information, the astute reader will realise that a stale record can potentially exist significantly *longer* than just the 14 days we've got configured here. If the record happens to hit the end of its refresh interval just *after* the scavenging process runs, in this configuration, the record could exist for up to an additional 7 days, in a sort of "grace period". Here's a graphical timeline of events:
<a href="https://flamingkeys.com/wp-content/uploads/2015/01/timeline.png"><img class="aligncenter size-full wp-image-889" src="{{ site.baseurl }}/assets/timeline.png" alt="timeline" width="973" height="109" /></a>
