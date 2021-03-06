---
layout: post
title: Enable Litigation Hold for all Mailboxes in Office 365
date: 2015-12-18 00:34:22.000000000 +11:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  _syntaxhighlighter_encoded: '1'
  og:image: ''
  og:description: ''
  og:title: ''
  _edit_last: '1'
  dsq_thread_id: '4413233775'
  _yoast_wpseo_content_score: '30'
  _yoast_wpseo_primary_category: ''
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/12/enable-litigation-hold-for-all-mailboxes-in-office-365/
---
There's no built-in feature at this time to enable litigation hold for all mailboxes in Exchange Online, but some organisations have this as a requirement. Here's a script that can be run ad-hoc to enable litigation hold for all mailboxes. When prompted, enter your Exchange Online credentials.

```powershell
# Connect to Exchange Online
$exo = New-PSSession -ConfigurationName Microsoft.Exchange `
-ConnectionUri https://ps.outlook.com/powershell-liveid `
-Authentication Basic `
-AllowRedirection `
-Credential $(Get-credential)

Import-PSSession $exo

# Get all mailboxes that do not have litigation hold enabled
$mailboxes = Get-Mailbox -ResultSize Unlimited -Filter {RecipientTypeDetails -eq "UserMailbox"} | 
Where {$_.LitigationHoldEnabled -ne $true}

# Enable litigation hold
$mailboxes | Set-Mailbox -LitigationHoldEnabled $true 

Remove-PSSession $exo 
```