---
layout: post
title: 'SCCM 2012 Distribution Point Certificate: Cannot access the file that you
  specified'
date: 2013-09-12 09:27:43.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Certificates
- Configuration Manager
- SCCM
- System Center
meta:
  dsq_thread_id: '1750132437'
  _edit_last: '1'
  amazonS3_cache: a:2:{s:55:"//flamingkeys.com/wp-content/uploads/2013/09/sccmdp.png";i:401;s:63:"//flamingkeys.com/wp-content/uploads/2013/09/sccmdp-300x112.png";i:401;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2013/09/sccm-2012-distribution-point-certificate-cannot-access-the-file-that-you-specified/
---
When trying to import a certificate into a SCCM 2012 distribution point, you may receive an error message: "Cannot access the file that you specified". This can occur even if you’ve specified a certificate on the local machine, and specified the correct password. It can even happen if you’re trying to change other configuration on a DP.

![]({{ site.baseurl }}/assets/sccmdp.png)

The reason for this error is that unless the Configuration Manager console is running as administrator, it cannot access the file. To make this work, simply right click the SCCM console and choose Run As Administrator. You will then be able to open it up and configure the certificate as necessary.
