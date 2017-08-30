---
layout: post
title: How to Grant Administrators Full Control over all mailboxes in an Organization
date: 2012-02-16 22:52:12.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Exchange
- Microsoft
- PowerShell
- Scripting
- Security
meta:
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '578260740'
  Hide OgTags: '0'
  Hide SexyBookmarks: '0'
  robotsmeta: index,follow
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Granting administrative rights over all mailboxes to an Exchange administrator was a simple task in Exchange 2003 and 2007, which requires a little more consideration. I’ve written a simple PowerShell script that will allow you to automate this process. As new mailboxes are created, this script will add the appropriate permissions. Please note that this script will only work (by default) on Exchange 2010 SP2. If you’re running RTM or SP1, please remove the `-AutoMapping:$false` parameter.

I schedule this script to run daily at 6AM. Your mileage may vary, but this allows it (and my other morning Exchange maintenance scripts) to finish before people start logging on around 7AM).

Note: I'd suggest you don't use this script if you use a moderate-large organisation. Assigning permissions on a one-off basis is a much more economical method.

{% highlight powershell %}
# Who should get access?
$mailadmins = @("chris")
# What do they need access to?
$rights = "fullaccess"
# Add Exchange 2010 snapins
if ( (Get-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction silentlycontinue) -eq $null )
{
  Write-Host "Importing Exchange modules" -BackgroundColor Black -ForegroundColor Yellow
  Add-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction SilentlyContinue
}
if ( (Get-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction silentlycontinue) -eq $null )
{
  throw "Unable to import exchange modules! Aborting..."
}
# Loop through each Mailbox
foreach ($mailbox in (Get-Mailbox -ResultSize unlimited)) {
  # Then loop through each mail admin in the array specified on line 2
  foreach ($mailAdmin in $mailAdmins) {
    # Take the mailbox and add the user to it with the specified rights.
    $mailbox | Add-MailboxPermission -User $mailadmin -AutoMapping:$false -AccessRights $rights
    # If using Exchange 2010 RTM or SP1, uncomment this line and comment out the one above:
    $mailbox | Add-MailboxPermission -User $mailadmin -AccessRights $rights
  }
}
{% endhighlight %}
