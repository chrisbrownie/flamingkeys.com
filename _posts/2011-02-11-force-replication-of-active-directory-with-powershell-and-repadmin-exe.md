---
layout: post
title: Force Replication of Active Directory with PowerShell and Repadmin.exe
date: 2011-02-11 19:26:52.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD-DS
- Microsoft
- PowerShell
- Scripting
- Server 2003
- Server 2008
- Server 2008 R2
- tips
- windows
meta:
  _sexybookmarks_shortUrl: http://fla.im/gSQiUW
  _sexybookmarks_permaHash: 61f6975d304fe59f16bedfcf1732729a
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '228763158'
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/02/force-replication-of-active-directory-with-powershell-and-repadmin-exe/
---

Perhaps you’re creating a user for immediate use in another site. Perhaps you’ve updated a group membership and accidentally used a DC in the wrong site. Maybe you’re just lazy? One of the most fun (read: boring) parts of Active Directory cross-site administration is replication. Opening up Active Directory Sites & Services and mindlessly clicking _Replicate Now_ gets painful, and begs the question of "how can I do this faster?". Along came Polly…That is, if by Polly you mean repadmin.exe.

[Repadmin](http://technet.microsoft.com/en-us/library/cc755360(WS.10).aspx) is the hidden treasure of AD-DS replication. It allows you to replicate a connection simply by executing two simple commands:

    repadmin /kcc MEL-DC01 MEL-DC02 SYD-DC01
    repadmin /syncall /A /e MEL-DC01

This is easy enough, but when you have more than three or four Active Directory domain controllers it, again, feels slow. Along came PowerShell. In the script below I use Quest’s [ActiveRoles Active Directory Management](http://www.quest.com/powershell/activeroles-server.aspx) Snap-in to find all domain controllers in the current domain and then replicate the connections to/from them all.

Once we have a list of DCs, it is possible to loop through each then recalculate the replication topology and then replicate the connections. This performs the same task as clicking _Replicate Now_ in `dssite.msc`.

## Replicate-ADDS.ps1

{% highlight powershell %}
# Transcribe output to log
$null = Start-Transcript "$pwd\$([System.IO.Path]::GetFileNameWithoutExtension($MyInvocation.MyCommand.Definition)).log"
# Check the QAD snapins are installed
if ( (Get-PSSnapin -Name Quest.ActiveRoles.ADManagement -ErrorAction silentlycontinue) -eq $null ) {
 # The QAD snapin is not active. Check it's installed
 if ( (Get-PSSnapin -Name Quest.ActiveRoles.ADManagement -Registered -ErrorAction SilentlyContinue) -eq $null) {
  Write-Error "You must install Quest ActiveRoles AD Tools to use this script!"
 } else {
  Write-Host "Importing QAD Tools"
  Add-PSSnapin -Name Quest.ActiveRoles.ADManagement -ErrorAction Stop
 }
}
Write-Host "Beginning ADDS Replication"
Write-Host "=========================="
# Find each domain controller, then do a foreach-object
Get-QADComputer -ComputerRole 'DomainController' | % {
 Write-Host "Replicating $($_.Name)"
 # Recalculate topology for this server
 $null = repadmin /kcc $_.Name
 # Replicate it
 $null = repadmin /syncall /A /e $_.Name
}
Write-Host "=========================="
Write-Host "Completed ADDS Replication"
Stop-Transcript
{% endhighlight %}