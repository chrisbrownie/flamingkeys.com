---
layout: post
title: Server 2008 R2 Wireless on Laptop
date: 2010-09-15 18:03:06.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Laptop
- Server 2008
- Server 2008 R2
- tips
- windows
- Wireless
meta:
  _edit_last: '1'
  _wp_old_slug: ''
  _sexybookmarks_shortUrl: http://bit.ly/gx89G3
  _sexybookmarks_permaHash: 6e5a51464d92240bf60546ea055e06e1
  dsq_thread_id: '205122668'
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
excerpt: This afternoon I decided to install Windows Server 2008 R2 on one of my laptops.
  Immediately it found most of the drivers, including my wireless network adapter.
  Fanatastic, says I. I was quickly lead to disappointment however as it could not
  find any wireless networks. This was confusing for a few moments but then I realised
  my iPhone 4 and ASUS EeePC were connected and working fine over my apartment's WiFi
  connection...
---
This afternoon I decided to install Windows Server 2008 R2 on one of my laptops. Immediately it found most of the drivers, including my wireless network adapter. Fanatastic, says I. I was quickly lead to disappointment however as it could not find any wireless networks. This was confusing for a few moments but then I realised my iPhone 4 and ASUS EeePC were connected and working fine over my apartment's WiFi connection.

I tried disabling and re-enabling the NIC, still couldn't see any networks. Rebooted the machine, updated the drivers, still no dice. To services.msc we go! Here I noticed the absence of ye old faithful 'WLAN AutoConfig' service.

This service (I soon discovered) is a piece of cake to install. Open up Server Manager and click on Features. Choose Add Features in the Features pane, then check the box for "Wireless LAN Service'. Click Next, then click Install.

Windows will chug away happily for a moment or two while it installs the service. Once it's completed click on Close. You will now be able to connect to your wireless network using the same method as other Windows 7/Vista devices.