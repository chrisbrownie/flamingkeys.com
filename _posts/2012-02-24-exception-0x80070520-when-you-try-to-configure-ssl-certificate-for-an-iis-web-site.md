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
The error text displayed when attempting to configure the binding is 

> There was an error while performing this operation. Details: A specified logon session does not exist. It may already have been terminated. (Exception from HRESULT: 0x80070520).

An audit failure is also created in the Security event log on the machine (Event ID 5061). 

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/02/image.png)

This error is caused as a result of your certificate not being marked exportable. To resolve this, simply reimport the certificate selecting the "Mark this key as exportable" option when importing the certificate:

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/02/image1.png)

The binding will now create successfully.
