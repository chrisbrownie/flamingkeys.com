---
layout: post
title: Exchange Management Console Command Reference
date: 2010-11-23 08:51:03.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- 2008 R2
- Computers
- Exch
- Exchange
- Microsoft
- PowerShell
- tips
- windows
meta:
  _edit_last: '1'
  _sexybookmarks_shortUrl: http://bit.ly/dGzHH5
  _sexybookmarks_permaHash: bff50a8738aaee86224692f147827434
  dsq_thread_id: '211051515'
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

Microsoft have taken a new approach with Exchange Management Console (EMC) for Microsoft Exchange Server 2010. Unlike Exchange 2007 (to a certain extent), almost every single management task is either a PowerShell command, or a front-end to a PowerShell command. Let’s say, for example, you want to create a new Mailbox. Easy, you say: Recipient Configuration, Mailbox, New Mailbox. Fill in the blanks and hit Next, right? This is great if you want to just create one or two users, but what if, like me, you need to create several hundred in one go? This could get tedious.

Any Exchange 2010 messaging administrator knows of this little gem tucked away in the corner of nearly every options window:

![]({{ site.baseurl }}/assets/psShowCommandButton.png)

Clicking this little icon in the bottom left corner will show you the PowerShell command(s) that EMC is about to execute.

![]({{ site.baseurl }}/assets/image.png)

Many, however, are not aware of another tool which is (in my opinion) even more useful. On the 'View' menu there is an option to _View Exchange Management Shell Command Log..._

![]({{ site.baseurl }}/assets/psCommandLog.png)

This little treasure allows you to view any and all PowerShell queries previously run by the Exchange Management Console (in this session). This is very useful if you (like me) spend many hours writing email reports and similar in PowerShell, spending most of your time guessing the queries. Simply open a dialog or screen, hit refresh if necessary (For example, Recipient Mailboxes needs refreshing occasionally), then open the Exchange Management Shell Command Log and scroll to the bottom.

![]({{ site.cdnbaseurl }}wp-content/uploads/2010/11/psCommandLogViewer.png)

From here you can paste (or retype, or modify, the list goes on) this command into Exchange Management Shell or into your .ps1 scripts. I hope this helps you gain more insight into what is a very powerful tool, and that you can save yourself some time by doing what all of us lazy messaging admins do – write scripts!
