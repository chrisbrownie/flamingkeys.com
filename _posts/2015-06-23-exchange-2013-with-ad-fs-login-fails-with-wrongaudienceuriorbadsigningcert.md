---
layout: post
title: Exchange 2013 with AD FS login fails with "WrongAudienceUriOrBadSigningCert"
date: 2015-06-23 03:29:22.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD FS
- Annoyances
- Exchange
- Microsoft
- PowerShell
- Server 2012 R2
meta:
  _edit_last: '1'
  _oembed_1e376e2fb011f8754b2b12406542229e: "{{unknown}}"
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3870887649'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
If you've attempted to configure Exchange Server 2013's OWA and EAC/ECP to authenticate using AD FS, you've probably had a pretty fun time of it. Microsoft's guides are good...but there's bits and pieces missing. 

I've been dealing with an error for a while now that has been a little annoying. When attempting to log in, after AD FS authentication, the user is redirected to `https://owa.contoso.com/owa/auth/errorfe.aspx?msg=WrongAudienceUriOrBadSigningCert` and is presented with the typical sad face emoticon and a pretty useless error message.

After a bit of digging, I discovered that **adding the AD FS token signing certificate to the Exchange Server(s)'s trusted root (not *my*) certificate store makes this work** almost immediately. 

I'm not sure why this is, but I'm positive it's not right. Interested to hear from anyone else who runs across this. My environment is Exchange Server 2013 CU9 on Windows Server 2012 R2 Standard, and AD FS 3.0 (on Windows Server 2012 R2 Standard).
