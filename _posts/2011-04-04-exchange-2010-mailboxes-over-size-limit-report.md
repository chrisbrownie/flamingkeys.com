---
layout: post
title: Exchange 2010 Mailboxes over Size Limit Report
date: 2011-04-04 11:47:21.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Exchange
- PowerShell
- Reporting
- tips
meta:
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: http://fla.im/hT70Oq
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: http://www.flamingkeys.com/2011/04/exchange-2010-mailboxes-over-size-limit-report/?utm_campaign=twitter&utm_medium=twitter&utm_source=twitter
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '270342931'
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/04/exchange-2010-mailboxes-over-size-limit-report/

---
Here's a fairly rudimentary report I threw together this morning that will notify my HelpDesk staff of Exchange 2010 Mailbox users over their mailbox size limit. I didn't feel that formatting or any fancy xml/xsl was necessary for this particular job. The [KISS](http://en.wiktionary.org/wiki/KISS) principle is strongly in play here. Here it is:

{% highlight powershell %}
# -------------------------------------------------------------------------------
# Script: Get-MailboxesOverSizeLimit.ps1
# Author: Chris Brown
# Date: 04/04/2011 10:41:00
# Keywords:
# comments:
#
# Versioning
# 04/04/2011  CJB  Initial Script
#
# -------------------------------------------------------------------------------

# -------------------------------------------------------------------------------
#                           ~ ~ ~ Variables ~ ~ ~
# ~~ Mail Server Details ~~
$SmtpServer = mailserver.yourdomain.local
$FromAddress = alerts@yourdomain.com
$ToAddress = HelpDesk@yourdomain.com
$Subject = [Exchange] Mailboxes over size limits
#
#
#-------------------------------------------------------------------------------

# Import the Exchange 2010 snap-in

if ( (Get-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction SilentlyContinue) -eq $null) {
 Write-Verbose "Exchange 2010 snapin is not loaded. Loading it now."
 try { 
   Add-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010; 
   Write-Verbose "Loaded Exchange 2010 snapin"
 }
 catch { 
   throw "Could not load Exchange 2010 snapins! $($_.ToString())" 
 }
}

$overLimit = Get-Mailbox | Get-MailboxStatistics -WarningAction SilentlyContinue | Where {ProhibitSend,MailboxDisabled -contains $_.StorageLimitStatus}

if ($overLimit.Count -gt 0) {
 $mailBody = $overLimit | ft DisplayName,TotalItemSize,StorageLimitStatus | Out-String
 Send-MailMessage -Body $mailBody.ToString() -From $FromAddress -SmtpServer $SmtpServer -Subject $Subject -To $toAddress
} else {
 "No results"
}
{% endhighlight %}
