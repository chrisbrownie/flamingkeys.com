---
layout: post
title: Determine which users have logged into Outlook Web App (OWA)
date: 2016-07-12 10:00:25.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Exchange
- Office 365
- PowerShell
- Scripting
- windows
meta:
  _yoast_wpseo_metadesc: How to determine which users have logged into Exchange or
    Exchange Online OWA versus those who haven't using Windows PowerShell.
  _yoast_wpseo_focuskw: owa
  _yoast_wpseo_focuskw_text_input: owa
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _yoast_wpseo_primary_category: '8'
  _yoast_wpseo_linkdex: '77'
  dsq_thread_id: '4978931131'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/07/determine-owa-users-powershell/
---
Discovering which users have logged into Outlook Web App (OWA) compared to those who haven't is a bit of a challenge in Exchange Online and Exchange Server 2013/2016. One method is to make use of the fact that prior to first login, a user won't have selected their language. Using this piece of knowledge allows us to perform a (somewhat rudimentary) evaluation of which users *probably* have logged into OWA.

# Connect to Exchange PowerShell and run the following:

{% highlight powershell %}
Get-Mailbox -ResultSize:unlimited | Get-MailboxRegionalConfiguration
{% endhighlight %}

Easy! This will show you each mailbox and by evaluating the "Language" value for each, you can determine if a user's logged into OWA or not. Let's make it even easier:

# The really easy way

{% highlight powershell %}
# Show me all the mailboxes that have probably not logged into OWA
Get-Mailbox -ResultSize:unlimited | Get-MailboxRegionalConfiguration | Where-Object {$null -eq $_.Language} 
# Tell me how many people have logged into OWA versus those who haven't (Those with a language will have a value, blanks have not logged in)
Get-Mailbox -ResultSize:unlimited | Get-MailboxRegionalConfiguration | Group-Object Language
{% endhighlight %}

<aside class="aside-warning">You'll note several occurrences of "probably" in this post. This is because some lovely Exchange Server admins like to preset regional settings for users as part of user deployment. This is a wonderful idea, and I strongly encourage it. Unfortunately, however, it breaks everything discussed above.</aside>

## Cmdlets used in this post

* [Get-Mailbox](https://technet.microsoft.com/en-us/library/bb123685(v=exchg.160).aspx)
* [Get-MailboxRegionalConfiguration](https://technet.microsoft.com/en-us/library/dd335163(v=exchg.160).aspx)
* [Group-Object](https://technet.microsoft.com/en-us/library/hh849907.aspx)
* [Where-Object](https://technet.microsoft.com/en-us/library/hh849715.aspx)
