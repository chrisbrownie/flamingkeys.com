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
---
When configuring split-scope DHCP you may encounter this error message:
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb.png" width="244" height="221" /></a>
This will occur if you have configured custom options in your DHCP server/scope. In this case, I have defined and configured option 252 (Proxy Configuration):
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image1.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb1.png" width="244" height="26" /></a>
This is visible by right-clicking “IPv4” under your DHCP server and choosing “Set Predefined Options”.
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image2.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb2.png" width="209" height="193" /></a>
I have defined option 252 as below:
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image3.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb3.png" width="244" height="241" /></a>
This option must now be duplicated on the new DHCP server. Again, open “Set Predefined Options…” under IPv4 on the new server. This time, click “Add” and copy the settings from your previous server. In my case:
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image4.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb4.png" width="244" height="141" /></a>
Running the wizard again, the scope is successfully split!
<a href="https://www.flamingkeys.com/wp-content/uploads/2011/09/image5.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb5.png" width="244" height="221" /></a>