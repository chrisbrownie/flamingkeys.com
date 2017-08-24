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
---
As a follow up to my <a href="https://www.flamingkeys.com/2010/12/iis-7-backup-and-restore/" target="_blank">previous post</a> regarding a GUI for backing up IIS, I thought it pertinent to cover how to back it up using the command line or <a href="https://www.flamingkeys.com/tag/PowerShell/" target="_blank">PowerShell</a>. This is a slightly more in-depth task than the earlier described method. There are four commands as part of the AppCmd backup module. These are list, add, restore and delete. AppCmd is located in %windir%\system32\inetsrv. You should set-location or cd to this directory before attempting to execute any of the commands below, or they will fail.

## AppCmd Add Backup

This method allows you to take a new backup of IIS. Execute the command as follows:

    C:\> appcmd add backup “backupname”

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image1.png)

This will create a new backup within IIS of your configuration with the title `backupname`. You can then use the following command to restore it:

## AppCmd Restore Backup

This method allows you to restore an existing backup of IIS. Execute the command as follows:

    C:\> appcmd restore backup “backupname”

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image2.png)

This will restore the backup called “backupname” from within IIS. Note that IIS will be restarted during this process. You can tell AppCmd not to restart IIS by issuing the following parameter on the end of the command: `/stop:false`.

## AppCmd List Backup

This command will allow you to list all stored backups. Issue as follows:

    C:\> appcmd list backup

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image3.png)

This command can be used to find backups you wish to restore or delete.

## AppCmd Delete Backup

To purge an existing backup, simply run:

    C:\> appcmd delete backup “backupname”

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image4.png)
As is evident, this buried tool is quite useful for anyone making significant changes to an IIS install.
For further information, you can always run

    C:\> appcmd backup /?

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image5.png)

and for each individual tool a

    C:\> appcmd add backup /?

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image6.png)

I hope this eases the pain of the otherwise complicated IIS backup situation.