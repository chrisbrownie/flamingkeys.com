---
layout: post
title: Exception 0x80070520 when you try to configure SSL certificate for an IIS Web
  Site
date: 2012-02-24 09:29:56.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Certificates
- IIS
- PKI
- SharePoint
meta:
  _edit_last: '1'
  dsq_thread_id: '587074867'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
The error text displayed when attempting to configure the binding is “There was an error while performing this operation. Details: A specified logon session does not exist. It may already have been terminated. (Exception from HRESULT: 0x80070520).” An audit failure is also created in the Security event log on the machine (Event ID 5061). 
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/02/image.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb.png" width="244" height="118" /></a>
This error is caused as a result of your certificate not being marked exportable. To resolve this, simply reimport the certificate selecting the “Mark this key as exportable” option when importing the certificate:
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/02/image1.png"><img style="background-image: none; border-bottom: 0px; border-left: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top: 0px; border-right: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb1.png" width="244" height="115" /></a>
The binding will now create successfully.
