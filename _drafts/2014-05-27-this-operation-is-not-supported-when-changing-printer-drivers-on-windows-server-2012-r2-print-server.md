---
layout: post
title: '"This operation is not supported" when changing printer drivers on Windows
  Server 2012 R2 Print Server'
date: 2014-05-27 03:30:09.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Computers
- Print Services
- Printers
- Server 2012 R2
- windows
meta:
  dsq_thread_id: '2715148246'
  _edit_last: '1'
  amazonS3_cache: a:2:{s:56:"//flamingkeys.com/wp-content/uploads/2014/05/Capture.png";i:814;s:64:"//flamingkeys.com/wp-content/uploads/2014/05/Capture-300x150.png";i:814;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
excerpt: Consider a scenario where you have a Windows Server 2012 R2 print server.
  When attempting to change the driver associated with a particular printer, you receive
  an error.
---
Consider a scenario where you have a Windows Server 2012 R2 print server. When attempting to change the driver associated with a particular printer, you receive the following error:
"Printer settings could not be saved. This operation is not supported."
<img src="{{ site.baseurl }}/assets/Capture.png" alt="Printer settings could not be saved" width="369" height="185" class="aligncenter size-full wp-image-814" />
This error occurs because the printer is shared. Simply unshare the printer and you will be able to change the printer driver. To unshare the printer, right click it in Print Management, then choose **Properties**. On the **Sharing** tab, uncheck the **Share this printer** box then click **Apply**. You will now be able to change the printer driver associated with this printer. Don't forget to re-share it when you're done!
