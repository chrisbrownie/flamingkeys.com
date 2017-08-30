---
layout: post
title: How to Reconnect a Disconnected Archive Mailbox in Exchange 2010
date: 2012-02-22 15:44:50.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Exchange
- Mailbox Recovery
- Personal Archives
- PowerShell
- windows
meta:
  dsq_thread_id: '584902163'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
When trying to connect an archive mailbox in Exchange 2010, you may receive an error
"The legacy DN ‘/O=ORG/OU=FIRST ADMINISTRATIVE GROUP/CN=RECIPIENTS/CN=KIM.AKERS’ of disconnected mailbox does not match user legacy dn ‘O=ORG/OU=Exchange Administrative Group (FYDIBOHF23SPDLT)/cn=Recipients/cn=Kim.Akerts639’.
<font color="#ff0000">**WARNING: This article contains instructions on using ADSIEdit and PowerShell to perform tasks that could endanger the livelihood of your Active Directory and Exchange environments. If you don’t know what ADSI, DN, OU and CN stand for (and what they do), I don’t recommend proceeding beyond here!**</font>
This is, in most instances, because the original user was created on in an Exchange 2003 administrative group and the new user was created on Exchange 2010 (or, presumably, 2007). For this reason their DNs will differ. Fortunately, this is a relatively simple issue to resolve. Simply change the new user’s DN to match the old one. This should not cause you any great issue. Changing the DN is a pretty easy task in ADSIEdit. Jump into ‘Default Naming Context’ and drill down to the new user. Open their properties dialog and scroll down Attribute Editor until you find the LegacyExchangeDN property. Adjust it to match the **first** DN you were presented (probably containing FIRST ADMINISTRATIVE GROUP or similar). Hit OK and close ADSIEdit. Now try to reattach the mailbox with:
{% highlight powershell %}Get-MailboxDatabase | Get-MailboxStatistics | Where-Object {$_.DisconnectDate –and $_.DisplayName –eq "Personal Archive – Kim Akers"} | Connect-Mailbox –user kim.akers –archive{% endhighlight %}
The mailbox will now attach, and once AD replicates, will begin to map to users as appropriate.
