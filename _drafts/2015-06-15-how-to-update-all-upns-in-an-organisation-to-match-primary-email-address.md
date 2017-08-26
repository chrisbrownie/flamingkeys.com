---
layout: post
title: How to Update All UPNs in an Organisation to Match Primary Email Address
date: 2015-06-15 09:45:14.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD-DS
- Automation
- Azure Active Directory
- Exchange
- Office 365
- PowerShell
- Scripting
- Security
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  dsq_thread_id: '3849661727'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
If you're transitioning to Office 365, Windows Azure Active Directory, or any other of the many ?aaS offerings that require routable <a href="https://msdn.microsoft.com/en-us/library/ms677605(v=vs.85).aspx" target="_blank">UPNs</a> (that is, a UPN with a **publicly resolvable domain name** **that you own**) you've likely stumbled across a need to change UPNs en masse. While ADUC gives us the option to change just the suffix for everyone in one go, many organisations need to change the left hand *name* part of this UPN.
Consider the following scenario:
<table>
<tbody>
<tr>
<td>User</td>
<td>Kim Akers</td>
</tr>
<tr>
<td>GivenName</td>
<td>Kim</td>
</tr>
<tr>
<td>Surname</td>
<td>Akers</td>
</tr>
<tr>
<td>sAMAccountName</td>
<td>KimA</td>
</tr>
<tr>
<td>UserPrincipalName</td>
<td>KimA@margiestravel.com</td>
</tr>
<tr>
<td>Email Address</td>
<td>Kim.Akers@margiestravel.com</td>
</tr>
</tbody>
</table>
This user's UPN has been generated based on her sAMAccountName, and it doesn't match her email address. She has no knowledge of what a UPN is, or when it should be used, and it doesn't make sense to educate on this. In this case, we should update Kim's UPN to be *kim.akers@margiestravel.com*, thus matching her email address.
***<span style="color: #ff0000;">Protip: Skip to the bottom for the tl;dr and script.</span>***
We could write a script to update everyone's UPNs to First.Last@margiestravel.com...or we could be a little more clever. Everyone's email address is stored in AD, why don't we just update their UPNs to that?
{% highlight powershell %}
$user = "KimA"
Get-ADUser $user -Properties proxyAddresses | Select -expand proxyAddresses
{% endhighlight %}
How does this work?
**$user** is a string variable storing Kim's username
**<a href="https://technet.microsoft.com/en-au/library/ee617241.aspx" target="_blank">Get-ADUser</a>** retrieves the AD user and some of its properties from AD. Since proxyAddresses isn't a default parameter returned by Get-ADUser we use the -Properties parameter to have it returned as well.
**<a href="https://technet.microsoft.com/en-us/library/hh849895.aspx" target="_blank">Select</a> -expand proxyAddresses** takes the *ADPropertyValueCollection* and expands it into a *System.Array* object that the rest of PowerShell can work with easily.
Output:
<pre>smtp:KimA@margiestravel.com
SMTP:Kim.Akers@margiestravel.com
smtp:accounts@margiestravel.com</pre>
We can see Kim has three email addresses. How do we translate this to something usable?
Let's try grabbing only Kim's primary email address*:
{% highlight powershell %}
Get-ADUser $user -Properties proxyAddresses | Select -Expand proxyAddresses | Where {$_ -clike "SMTP:*"}
{% endhighlight %}
**<a href="https://technet.microsoft.com/en-au/library/ee177028.aspx" target="_blank">where</a>** allows us to filter the results.
**-clike** performs a case-sensitive like statement, where * is the wildcard character.
** I know I can do this using the *mail* property, but I like to assume it's incorrect for any number of possible reasons. Exchange honours the proxyAddresses value, so we will too.*
Great, now let's clean it up a bit and remove the protocol specifier at the start:
{% highlight powershell %}
$address = Get-ADUser $user -Properties proxyAddresses | Select -Expand proxyAddresses | Where {$_ -clike "SMTP:*"}
$address = $address.SubString(5)
{% endhighlight %}
**<a href="https://msdn.microsoft.com/en-us/library/system.string.substring(v=vs.110).aspx" target="_blank">.SubString</a>** allows us to select all characters in the string starting at the passed value (5).
Now we can put it all together and update Kim's UPN:
{% highlight powershell %}
$User = "KimA"
$address = Get-ADUser $user -Properties proxyAddresses | Select -Expand proxyAddresses | Where {$_ -clike "SMTP:*"}
$address = $address.SubString(5)
Set-ADUser $user -UserPrincipalName $address
{% endhighlight %}
**<a href="https://technet.microsoft.com/en-au/library/ee617215.aspx" target="_blank">Set-ADUser</a>** applies the specified update (-UserPrincipalName) to the existing user.
Voila! Kim's UPN is updated. Now let's, through the magic of <del>television</del>PowerShell, expand this script a little to work for *all* my users:
{% highlight powershell %}
# Get all the users who have proxyAddresses under the margiestravel.com domain
foreach ($user in (Get-ADUser -SearchBase "DC=MargiesTravel,DC=com" -LdapFilter '(proxyAddresses=*)')) {
	# Grab the primary SMTP address
	$address = Get-ADUser $user -Properties proxyAddresses | Select -Expand proxyAddresses | Where {$_ -clike "SMTP:*"}
	# Remove the protocol specification from the start of the address
	$newUPN = $address.SubString(5)
	# Update the user with their new UPN
	Set-ADUser $user -UserPrincipalName $newUPN
}
{% endhighlight %}
This script requires that all primary email domains are present in AD as UPN suffixes. You can add additional UPN suffixes easily with PowerShell:
{% highlight powershell %}
Set-ADForest -UPNSuffixes @{Add="margiestravel.com"}
{% endhighlight %}
