---
layout: post
title: Enable IMAP on all Exchange 2013/2016 Servers
date: 2016-08-04 04:58:28.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Automation
- Exch
- Exchange
- Microsoft
- PowerShell
- Scripting
- windows
meta:
  _edit_last: '1'
  _yoast_wpseo_content_score: '60'
  _yoast_wpseo_primary_category: '8'
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '5038760827'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/08/enable-imap-exchange-servers/
---
IMAP and POP3 are disabled on Exchange Server 2013 and 2016 servers by default. Chances are if you need to enable it, you'll need to enable it on all your servers at once. Fortunately, PowerShell makes this easy!

Run Exchange Management Shell as administrator on an Exchange server and execute the following:

# PowerShell Script to enable IMAP on all servers

```powershell
$Servers = Get-ExchangeServer | ? IsE15OrLater | Select -exp Name
Get-Service -ComputerName $servers -Name “MSExchangeIMAP4”,”MSExchangeIMAP4BE” | Set-Service -StartupType Automatic
Get-Service -ComputerName $servers -Name “MSExchangeIMAP4”,”MSExchangeIMAP4BE” | Start-Service
```
