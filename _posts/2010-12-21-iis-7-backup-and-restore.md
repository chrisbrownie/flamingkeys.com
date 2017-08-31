---
layout: post
title: IIS 7 Backup and Restore
date: 2010-12-21 10:05:26.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Backup
- Exchange
- IIS
- Server 2008 R2
- tips
- windows
meta:
  _edit_last: '1'
  _sexybookmarks_shortUrl: http://bit.ly/fO35Cx
  _sexybookmarks_permaHash: 187d6320b29219150fca46318689056b
  dsq_thread_id: '205526823'
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

With Exchange becoming only more integrated with Microsoft’s web server, [Internet Information Services](http://en.wikipedia.org/wiki/Internet_Information_Services), it is becoming a more and more critical service. One thing I’ve always wanted, but have never been satisfied with, is the backup and restore capabilities (or lack thereof) built into IIS. Apache is a very simple web server to back up, as everything is flat file. On the other hand, IIS uses a combination of the registry and file configuration. This is just a graphical approach to the "appcmd" functionality of IIS.

For this reason, I was quite excited when I found [this blog post](http://blogs.msdn.com/b/rakkimk/archive/2007/11/04/iis7-backup-restore-ui-module.aspx) by [Rakki Muthukumar](http://blogs.msdn.com/b/rakkimk) over at MSDN blogs. Rakki has written a module for IIS 7 (which also works with 7.5) to allow for simple backing up and restoring of IIS. Simply download the extension [here](http://www.iis.net/community/default.aspx?tabid=34&amp;g=6&amp;i=1552) to get started.
Installation is not trivial but should not pose any problem to those familiar with the keyboard and mouse. From a command line run `gacutil –i IIS7BackupRestore.dll` to insert it into your [Global Assembly Cache](http://msdn.microsoft.com/en-us/library/yf1d93sz.aspx). Secondly, open `%WINDIR%\System32\InetSrv\config\Administration.config` in your favourite text editor, then inside the `<moduleProviders>` section, add the following line:

{% highlight xml %}
<add name="IIS7BackupRestoreUI" type="IIS7BackupRestoreUI.MyModuleProvider, IIS7BackupRestoreUI, Version=1.0.0.0, Culture=neutral, PublicKeyToken=db9daa3d2ea5f6fd" />
{% endhighlight %}

Inside the `<modules>` section of the same file, add the following line:

{% highlight xml %}
<add name="IIS7BackupRestoreUI" />
{% endhighlight %}

Restart IIS Manager and you’ll see the module here:
![]({{ site.cdnbaseurl }}wp-content/uploads/2010/12/image.png)

One thing to note is that the module (at least graphically) does not give you the option to delete or rename the backups, so take a moment to decide on a naming convention. I’d suggest something like `Date – Action`. For example `2011-12-21-NewSSLCertificate`.
