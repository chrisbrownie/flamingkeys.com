---
layout: post
title: Bulk convert AD groups
date: 2015-03-24 08:00:57.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD-DS
- Automation
- Computers
- Microsoft
- PowerShell
- Scripting
- Server 2008
- Server 2008 R2
- Server 2012 R2
- windows
meta:
  dsq_thread_id: '3621269113'
  og:image: ''
  og:description: ''
  _edit_last: '1'
  og:title: ''
  _syntaxhighlighter_encoded: '1'
  _oembed_64e3573ade82b31938b4ed6535a50a13: "{{unknown}}"
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
With the advent of Windows PowerShell, performing bulk tasks just gets easier and easier. Consider a scenario where you have a bunch of Domain Local groups that you need to convert to Global...or any other type of conversion. This is a task that can take a large amount of time to complete manually, even with copious amounts of coffee and House of Cards on the TV. Fortunately, a few lines of PowerShell can make short work of this.
Consider the aforementioned Domain Local to Global conversion. As the astute reader will know, a Domain Local to Global conversion isn't a one-step process. Each group must first be converted to Universal. Why this caveat exists is far too detailed to discuss here, but Microsoft have a pretty ok writeup of it <a title="Group scope" href="https://technet.microsoft.com/en-us/library/cc755692(v=ws.10).aspx" target="_blank">here</a>.
{% highlight powershell %}
# Get all the groups in the OU we're targeting
$groups = Get-ADGroup -Filter * -SearchBase "OU=File Shares,OU=Groups,DC=Contoso,DC=com"
# Recurse through each group
Foreach ($group in $groups) {
  # Make it universal
  $group | Set-ADGroup -GroupScope 2
  # Make it global
  $group | Set-ADGroup -GroupScope 1
}
{% endhighlight %}
This code isn't really optimised for any more than a few hundred groups, so buyer beware when running this across a large number of groups.
