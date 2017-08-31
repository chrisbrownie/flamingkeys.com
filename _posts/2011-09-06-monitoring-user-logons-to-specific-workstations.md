---
layout: post
title: Monitoring User Logons to Specific Workstations
date: 2011-09-06 09:30:44.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD-DS
- Group Policy
- Microsoft
- PowerShell
- Scripting
- tips
meta:
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
  robotsmeta: index,follow
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '405705725'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/09/monitoring-user-logons-to-specific-workstations/
---
The script and group policy below will allow you to monitor a specific group of users logging on to certain workstations/computers. This is helpful if you believe users are logging onto machines in an unusual pattern or to workstations they probably shouldn’t be. Of course you can lock users down to specific machines, but in this situation, that wasn’t an option for me.

# NotifyLogon.ps1

{% highlight powershell %}
$mailBody = "[{0}] {1} has logged on to {2}" -f $(Get-Date),$env:USERNAME,$env:COMPUTERNAME
$mailSubject = "[{0}] {1}" -f $env:COMPUTERNAME,$env:USERNAME
Send-MailMessage -Body $mailBody -From logonalerts@contoso.com -SmtpServer "mailserver.contoso.com" -Subject $mailSubject -To helpdesk@contoso.com 
{% endhighlight %}

This script sends a basic email in the format:

> [17/08/2011 4:31:29 PM] kim.akers has logged on to KIOSK14

Next, create a group policy to assign this as a logon script for users when they log onto machines in the specified OU:

*Computer Configuration\Policies\Administrative Templates\System\Group Policy\User Group Policy loopback processing mode: Enabled (Merge)*
*User Configuration\Policies\Windows Settings\Scripts\Logon: NotifyLogon.ps1*

Link this policy to the OU containing the **Computers** you wish to monitor, and set your security filtering to allow the policy to apply to these Computers (I use the `Domain Computers` group) and the Users whom you wish to monitor.
