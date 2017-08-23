---
layout: post
title: 2008 R2 Domain Controller hangs during Applying Computer Settings
date: 2010-09-29 23:24:59.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD-DS
- DNS
- FSMO
- Server 2008 R2
meta:
  _edit_last: '1'
  _wp_old_slug: ''
  _sexybookmarks_shortUrl: http://bit.ly/egTg1W
  _sexybookmarks_permaHash: 865eae8315f40aa479e1fe6657d9b8d1
  dsq_thread_id: '206366111'
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
excerpt: Today I was faced with a strange problem. One of our domain controllers (essentially
  the 'primary' one, all FSMO roles, DNS, etc) stopped letting me make changes to
  its Netlogon share. I thought "hmm that's weird" and rebooted it, assuming it would
  be back up in a matter of minutes. Oh how wrong I was. After 20 minutes this server
  was not back up, which had me slightly concerned...
---

Today I was faced with a strange problem. One of our domain controllers (essentially the 'primary' one, all FSMO roles, DNS, etc) stopped letting me make changes to its Netlogon share. I thought "hmm that's weird" and rebooted it, assuming it would be back up in a matter of minutes. Oh how wrong I was. After 20 minutes this server was not back up, which had me slightly concerned. After making the 5 minute trip to the server room (no iLOM on this server) I saw it had hung on Windows Server 2008 R2's "Applying Computer Settings" screen. I rebooted it again, hoping it would come up. Again, it appeared to hang on this screen. I was able to ping it from other machines, but it seemed the AD-DS services were not up as I could not access it via RPC, UNC, RDP etc.

I am usually a fairly calm person, I can generally attack issues with a fairly level head, but by this point I was dripping with a very cold sweat (no mean feat in a 17 degree Celsius server room). After about half an hour on this screen the box finally reached a logon screen where I was able to logon (albeit, cached credentials only). Once it let me log on (10 minute wait) I was able to view the event logs and see error after error. After googling away with these error messages everyone said "just netdom the machine out of the domain then re-join it!" which sounds like a great idea, except the fact this is a domain controller holding all FSMO roles (particularly RID, Infrastructure and Schema masters). This is not the sort of computer you can just rip out of the domain and re-join. AD-DS was failing to start, as was DNS. I can deal with AD-DS not running as we have 6 other domain controllers at present ready to jump in and take over, but DNS is a critical service on this server (I will post the actual error messages when I grab the logs).

After much sweating and several reboots I discovered the DNS database on this machine was corrupt (causing an infinite loop - AD waiting for DNS, and DNS needs AD before it can start) , and as it was authoritative for the domain did not have a Master server to refresh its database from. From here I was able to re-point the machine to use another domain controller as its upstream DNS, wait for the DNS zone to reload, then reboot the machine successfully.

All is working now, but hopefully if you've found this article it can be of some help regarding any problems you're having… I know how we sysadmins love to be lazy, and the more info on the internet the better, I say!
