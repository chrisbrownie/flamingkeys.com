---
layout: post
title: Enable HSTS with NetScaler
date: 2016-08-04 04:37:54.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- NetScaler
- Security
meta:
  _edit_last: '1'
  _yoast_wpseo_focuskw_text_input: HSTS
  _yoast_wpseo_focuskw: HSTS
  _yoast_wpseo_linkdex: '61'
  _yoast_wpseo_content_score: '90'
  _yoast_wpseo_primary_category: '8'
  dsq_thread_id: '5038731272'
  amazonS3_cache: a:4:{s:66:"//flamingkeys.com/wp-content/uploads/2016/08/flamingkeys-aplus.png";i:1156;s:75:"//flamingkeys.com/wp-content/uploads/2016/08/flamingkeys-aplus-1024x524.png";i:1156;s:82:"//static.flamingkeys.com/wp-content/uploads/2016/08/04043402/flamingkeys-aplus.png";i:1156;s:91:"//static.flamingkeys.com/wp-content/uploads/2016/08/04043402/flamingkeys-aplus-1024x524.png";i:1156;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
HTTP Strict Transport Security, or HSTS, is a good way to help ensure visitors to your site do so using a secure connection. On top of this, it's a great way to ensure you get that all-important A+ score on Qualys.
<img src="{{ site.baseurl }}/assets/flamingkeys-aplus-1024x524.png" alt="flamingkeys-aplus" width="550" height="281" class="aligncenter size-large wp-image-1156" />
If you're running a NetScaler in front of your service, you may want to configure these headers to appear care of the Virtual Server serving the content, rather than the back-end service or service group. This is quite simple using a rewrite policy.
<h2>NetScaler Rewrite Policy to enable HSTS</h2>
<code>
add rewrite action RW_ACT_HSTS insert_http_header Strict-Transport-Security "\"max-age=157680000\""
add rewrite policy RW_POL_HSTS true RW_ACT_HSTS
bind lb vserver vs_remote -policy RW_POL_HSTS -priority 100 -gotoPriorityExpression END -type RESPONSE
</code>
All responses through this vServer will now have the HSTS header attached. You can (and should) change the max-age to your preferred value.
Thanks to <a href="https://ivancacic.com" target="_blank">Ivan Cacic</a> for this tip!
