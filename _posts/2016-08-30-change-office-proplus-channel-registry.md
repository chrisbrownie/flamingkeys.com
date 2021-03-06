---
layout: post
title: Change Office ProPlus Channel with Registry Keys
date: 2016-08-30 01:21:58.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Office 365
meta:
  _yoast_wpseo_content_score: '30'
  _yoast_wpseo_primary_category: '8'
  _yoast_wpseo_linkdex: '37'
  _yoast_wpseo_focuskw: channel
  _yoast_wpseo_focuskw_text_input: channel
  _edit_last: '1'
  dsq_thread_id: '5104873746'
  _thumbnail_id: '1166'
  amazonS3_cache: a:2:{s:69:"//flamingkeys.com/wp-content/uploads/2016/08/officepropluschannel.png";i:1166;s:85:"//static.flamingkeys.com/wp-content/uploads/2016/08/30012311/officepropluschannel.png";i:1166;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
excerpt: Changing the Office 365 ProPlus branch is easy using the registry, group
  policy, or the Office Customization Tool
redirect_from:
  - /2016/08/change-office-proplus-channel-registry/
---
The [Office Customization Tool or Group Policy](https://technet.microsoft.com/en-au/library/mt455210.aspx) can be used to change the branch of Office 365 ProPlus between any of the options (Current, Deferred, First Release for Deferred). The following registry keys can be applied to allow this without needing to use either Group Policy or the OCT.
Using regedit or PowerShell, open the path `HKLM\SOFTWARE\Policies\Microsoft\Office\16.0\Common\OfficeUpdate` and create the following values:

| Value | Data
| --- | ---
| `updatebranch` | A string (REG_SZ) value specifying the name of the branch (e.g. "Current")
| `EnableAutomaticUpdates` | A hexadecimal (REG_DWORD) value specifying whether automatic updates are enabled or not (set 0 for disabled, 1 for enabled)

Mine looks like this:
![]({{ site.baseurl }}/assets/officepropluschannel.png)

