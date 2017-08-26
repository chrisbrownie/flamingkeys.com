---
layout: post
title: How to Disable Server Manager Globally on Server 2008 RTM or R2
date: 2010-09-07 14:40:42.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Group Policy
- Registry
- Server 2008
- Server 2008 R2
- Server Manager
meta:
  _edit_last: '1'
  _thumbnail_id: '23'
  _wp_old_slug: ''
  _sexybookmarks_shortUrl: http://bit.ly/gXaiIH
  _sexybookmarks_permaHash: 42de9db8fb15cddef958fe79024efa32
  dsq_thread_id: '207680406'
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
  amazonS3_cache: a:3:{s:66:"//flamingkeys.com/wp-content/uploads/2010/09/ServerManagerTask.png";i:23;s:74:"//flamingkeys.com/wp-content/uploads/2010/09/ServerManagerTask-129x150.png";i:23;s:74:"//flamingkeys.com/wp-content/uploads/2010/09/ServerManagerTask-259x300.png";i:23;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
excerpt: For a reason too boring for me to describe, I had a need on a 2008 terminal
  server to disable Server Manager without using group policy. Without getting drastic
  and deleting all references to it, I found the following little gem buried within
  Windows Server 2008’s Task Scheduler.
---

For a reason too boring for me to describe, I had a need on a 2008 terminal server to disable Server Manager without using group policy. Without getting drastic and deleting all references to it, I found the following little gem buried within Windows Server 2008’s Task Scheduler.

Open `Task Scheduler (Local)` > `Task Scheduler Library` > `Microsoft` > `Windows` > `Server Manager` you will see a task entitled *Server Manager*. It is scheduled to trigger at the log on of _any_ user. That is; even after you disable it by ticking the "Do not show me this console at logon" checkbox it is still loaded, solely for the purpose of checking if you have ticked it. Disable the aforementioned task and you and your users will no longer be bothered by this annoying utility.

![Server Manager Task]({{ site.baseurl }}/assets/ServerManagerTask-259x300.png)
