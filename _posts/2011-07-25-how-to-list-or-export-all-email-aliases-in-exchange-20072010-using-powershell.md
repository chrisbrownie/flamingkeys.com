---
layout: post
title: How to list or export all Mailbox Email Addresses in Exchange 2007/2010 using
  PowerShell
date: 2011-07-25 00:03:19.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Automation
- Exchange
- PowerShell
- Scripting
- Server 2008 R2
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
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '367071244'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
This one comes up quite often, so I figured I'd make a quick and easy script for it. The first step is to make sure you're on a machine with the Exchange Management Tools installed, and logged on as a user with privileges to read mailbox properties. If you don’t want to read the explanation, skip ahead to The Script.
First, let’s create an empty array to store the results in:

{% highlight powershell %}
$addresses = @()
{% endhighlight %}

Next, we’ll grab all the mailboxes from the organisation. By default Exchange will only return the first 1000 results, so we need to specify the `ResultSize` parameter to ensure all mailboxes are counted.

{% highlight powershell %}
$Mailboxes = Get-Mailbox -ResultSize Unlimited
{% endhighlight %}

Now loop through each mailbox using a `ForEach` statement:

{% highlight powershell %}
ForEach ($mbx in $Mailboxes) {}
{% endhighlight %}

Per each mailbox, let’s loop through the email addresses. Exchange stores SMTP, X400, SIP and any number of other address types in this property. We’re only interested in SMTP addresses, so we’ll use an if statement inside this loop. Because Exchange may qualify the address with either `SMTP:` or `smtp:` we’ll convert the prefix to lower and then do the compare:

{% highlight powershell %}
ForEach ($address in $mbx.EmailAddresses) {
    if ($address.ToString().ToLower().StartsWith(“smtp:”)) {}
}
{% endhighlight %}

Doing this has allowed us to only inspect SMTP addresses. Now we can create an object and insert the appropriate variables in it. Finally, we’ll append it to the `$addresses` array we created earlier. We’ll use SubString to retrieve only characters after the 5th position from the email address (`smtp:kim.akers@contoso.com` will become `kim.akers@contoso.com`):

{% highlight powershell %}
$obj = “” | Select-Object Alias,EmailAddress
$obj.Alias = $mbx.Alias
$obj.EmailAddress = $address.ToString().SubString(5)
$addresses += $obj
{% endhighlight %}

Now we have an object, `$addresses` which we can export and/or format in any number of ways. In my example below, I have exported it to CSV then opened the CSV (using `Invoke-Item`) with its default handler. You could also use Format-Table, Format-List and any number of different methods. You can even interact with this array in other cmdlets or pass it down the pipeline. The possibilities are endless!

## The Script

{% highlight powershell %}
# -------------------------------------------------------------------------------
# Script: Get-AllEmailAddresses.ps1
# Author: Chris Brown http://www.flamingkeys.com/
# Date: 25/07/2011 00:03
# Keywords: Exchange, Email, SMTP
# comments:
#
# Versioning
# 25/07/2011  CJB  Initial Script
#
# -------------------------------------------------------------------------------
# Import the E2010 modules if available, otherwise import 2007's.
if (Get-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010 -Registered -ErrorAction SilentlyContinue) {
	# Found 2010, add it
	Add-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010
} else {
	# Add 2007
	Add-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.Admin
}
# Create an object to hold the results
$addresses = @()
# Get every mailbox in the Exchange Organisation
$Mailboxes = Get-Mailbox -ResultSize Unlimited
# Recurse through the mailboxes
ForEach ($mbx in $Mailboxes) {
	# Recurse through every address assigned to the mailbox
	Foreach ($address in $mbx.EmailAddresses) {
		# If it starts with &quot;SMTP:&quot; then it's an email address. Record it
		if ($address.ToString().ToLower().StartsWith(&quot;smtp:&quot;)) {
			# This is an email address. Add it to the list
			$obj = &quot;&quot; | Select-Object Alias,EmailAddress
			$obj.Alias = $mbx.Alias
			$obj.EmailAddress = $address.ToString().SubString(5)
			$addresses += $obj
		}
	}
}
# Export the final object to a csv in the working directory
$addresses | Export-Csv addresses.csv -NoTypeInformation
# Open the csv with the default handler
Invoke-Item addresses.csv
{% endhighlight %}
