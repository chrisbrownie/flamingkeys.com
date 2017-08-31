---
layout: post
title: Split-Scope DHCP Error 0x00004E2A The specified option does not exist
date: 2011-09-27 11:24:42.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- DHCP
- DR
- Microsoft
- Server 2008 R2
- windows
meta:
  _wp_jd_target: ''
  _wp_jd_bitly: ''
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  dsq_thread_id: '426802923'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/09/split-scope-dhcp-error-0x00004e2a-the-specified-option-does-not-exist/
---
When configuring split-scope DHCP you may encounter this error message: 

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image.png) 

This will occur if you have configured custom options in your DHCP server/scope. In this case, I have defined and configured option 252 (Proxy Configuration): 

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image1.png) 

This is visible by right-clicking "IPv4" under your DHCP server and choosing "Set Predefined Options". 

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image2.png) 

I have defined option 252 as below: 

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image3.png) 

This option must now be duplicated on the new DHCP server. Again, open "Set Predefined Options…" under IPv4 on the new server. This time, click "Add" and copy the settings from your previous server. In my case: 

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image4.png) 

Running the wizard again, the scope is successfully split!

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/09/image5.png)