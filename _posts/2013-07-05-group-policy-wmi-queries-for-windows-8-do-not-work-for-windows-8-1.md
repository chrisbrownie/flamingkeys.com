---
layout: post
title: Group Policy WMI Queries for Windows 8 do not work for Windows 8.1
date: 2013-07-05 09:19:50.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- Group Policy
- Query
- Windows 8
- Windows 8.1
- WMI
meta:
  dsq_thread_id: '1467776548'
  _edit_last: '1'
  amazonS3_cache: a:2:{s:54:"//flamingkeys.com/wp-content/uploads/2013/07/image.png";i:392;s:60:"//flamingkeys.com/wp-content/uploads/2013/07/image_thumb.png";i:393;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
With Windows 8.1, Microsoft have increased the operating system’s internal version number from Windows 8’s `6.2.x` to `6.3.x`. This means that if you have any "Windows 8" WMI queries, they’ll fail for 8.1. You can use the following query to get around this:

{% highlight sql %}
SELECT * FROM Win32_OperatingSystem WHERE (Version LIKE "6.2.%" OR Version LIKE "6.3.%") AND ProductType="1"
{% endhighlight %}

This query will return both Windows 8 and 8.1, but not Windows 7 (6.1) or Vista (6.0).

![]({{ site.baseurl }}/assets/image_thumb.png)
