---
layout: post
title: How to Back up IIS 7 and 7.5 with AppCmd
date: 2010-12-21 13:11:38.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Backup
- Exchange
- IIS
- Microsoft
- PowerShell
- Server 2008
- Server 2008 R2
meta:
  _edit_last: '1'
  _sexybookmarks_shortUrl: http://bit.ly/i4pvFI
  _sexybookmarks_permaHash: d1d48ad400b13edcfdbfe67dcc340b1a
  dsq_thread_id: '205120201'
  robotsmeta: index,follow
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
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2010/12/back-up-iis-7-and-7-5-with-appcmd/
---
As a follow up to my [previous post](/iis-7-backup-and-restore) regarding a GUI for backing up IIS, I thought it pertinent to cover how to back it up using the command line or PowerShell. This is a slightly more in-depth task than the earlier described method. There are four commands as part of the AppCmd backup module. These are list, add, restore and delete. AppCmd is located in %windir%\system32\inetsrv. You should set-location or cd to this directory before attempting to execute any of the commands below, or they will fail.

## AppCmd Add Backup

This method allows you to take a new backup of IIS. Execute the command as follows:

<pre><code class="dos">C:\> appcmd add backup "backupname"</code></pre>

![]({{ site.baseurl }}/assets/backup-iis-001.png)

This will create a new backup within IIS of your configuration with the title `backupname`. You can then use the following command to restore it:

## AppCmd Restore Backup

This method allows you to restore an existing backup of IIS. Execute the command as follows:

<pre><code class="dos">C:\> appcmd restore backup "backupname"</code></pre>

![]({{ site.baseurl }}/assets/backup-iis-002.png)

This will restore the backup called "backupname" from within IIS. Note that IIS will be restarted during this process. You can tell AppCmd not to restart IIS by issuing the following parameter on the end of the command: `/stop:false`.

## AppCmd List Backup

This command will allow you to list all stored backups. Issue as follows:

<pre><code class="dos">C:\> appcmd list backup</code></pre>

![]({{ site.baseurl }}/assets/backup-iis-003.png)

This command can be used to find backups you wish to restore or delete.

## AppCmd Delete Backup

To purge an existing backup, simply run:

    C:\> appcmd delete backup "backupname"

![]({{ site.baseurl }}/assets/backup-iis-004.png)
As is evident, this buried tool is quite useful for anyone making significant changes to an IIS install.
For further information, you can always run

    C:\> appcmd backup /?

![]({{ site.baseurl }}/assets/backup-iis-005.png)

and for each individual tool a

    C:\> appcmd add backup /?

![]({{ site.baseurl }}/assets/backup-iis-006.png)

I hope this eases the pain of the otherwise complicated IIS backup situation.
