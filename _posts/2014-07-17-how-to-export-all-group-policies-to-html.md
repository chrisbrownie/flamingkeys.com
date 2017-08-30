---
layout: post
title: How to export all group policies to HTML
date: 2014-07-17 07:53:33.000000000 +10:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '2850398437'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Exporting all group policies to HTML is an arduous task at best...until you open up Windows PowerShell. No matter how counter-intuitive the `GroupPolicy` module for Windows Server may be, it's quite simple to export all GPOs:

{% highlight powershell %}
Get-GPO -all | % { Get-GPOReport -GUID $_.id -ReportType HTML -Path "C:\GPOExport\$($_.displayName).html" }
{% endhighlight %}

Piece of cake! Automate this for regular, human-readable, Group Policy backups.
