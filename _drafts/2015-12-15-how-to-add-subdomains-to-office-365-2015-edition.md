---
layout: post
title: How to add subdomains to Office 365 (2015 Edition)
date: 2015-12-15 10:04:00.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Azure AD
- Office 365
- PowerShell
meta:
  og:description: ''
  og:image: ''
  og:title: ''
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '4405366076'
  amazonS3_cache: a:3:{s:54:"https://msdn.microsoft.com/en-us/library/dn194090.aspx";a:1:{s:9:"timestamp";i:1478085360;}s:54:"https://msdn.microsoft.com/en-us/library/dn194123.aspx";a:1:{s:9:"timestamp";i:1478085360;}s:54:"https://msdn.microsoft.com/en-us/library/dn194081.aspx";a:1:{s:9:"timestamp";i:1478085360;}}
  _yoast_wpseo_content_score: '30'
  _yoast_wpseo_primary_category: ''
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
If you've found this post, you've probably tried to add a subdomain to Office 365 and run across this error:
> sub.contoso.com is a subdomain of a domain that was added by using the Microsoft Online Services Module for Windows PowerShell so you'll need to also use Windows PowerShell to add sub.contoso.com to Microsoft Online Services.
or similar.
The following commands should be used from the <a href="http://aka.ms/aadposh" target="_blank">Windows Azure Active Directory PowerShell Module</a> to resolve this:
**For a federated domain (AD FS):**
https://gist.github.com/chrisbrownie/3e7bead963dc797a4b443644f1dce8d1
**For a managed (standard, in cloud, non-federated, password sync, cloud only, whatever you'd like to call it) domain:**
https://gist.github.com/chrisbrownie/32f62e2082681825b8f75f34fbfcde1e
You can use <a href="https://msdn.microsoft.com/en-us/library/dn194090.aspx" target="_blank">Get-MsolDomain</a> to validate this configuration
Keep in mind that subdomains *must* use the same authentication method as any parent domains.
## Cmdlets used in this post:
<ul>
<li><a href="https://msdn.microsoft.com/en-us/library/dn194123.aspx" target="_blank">Connect-MsolService</a></li>
<li><a href="https://msdn.microsoft.com/en-us/library/dn194081.aspx" target="_blank">New-MsolDomain</a></li>
<li><a href="https://msdn.microsoft.com/en-us/library/dn194090.aspx" target="_blank">Get-MsolDomain</a></li>
</ul>
