---
layout: post
title: Why Execution Policy is not Security
date: 2016-01-21 01:08:26.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Microsoft
- PowerShell
- Scripting
- Security
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '4509213769'
  amazonS3_cache: a:6:{s:90:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_02_09-CDC1-on-172.16.10.240.png";i:1105;s:90:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_06_29-CDC1-on-172.16.10.240.png";i:1106;s:97:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_02_09-CDC1-on-172.16.10.240-300x35.png";i:1105;s:97:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_02_09-CDC1-on-172.16.10.240-768x91.png";i:1105;s:97:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_02_09-CDC1-on-172.16.10.240-730x86.png";i:1105;s:97:"//flamingkeys.com/wp-content/uploads/2016/01/2016-01-21-12_06_29-CDC1-on-172.16.10.240-300x21.png";i:1106;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/01/why-execution-policy-is-not-security/
---
The PowerShell execution policy seems to be widely misunderstood. It is not there to stop administrators doing things. At all.
This is why:

{% highlight powershell %}
C:\> .\badscript.ps1
{% endhighlight %}


![]({{ site.baseurl }}/assets/2016-01-21-12_02_09-CDC1-on-172.16.10.240.png)

Cool! Can't run the script! Right?
Wrong.

{% highlight powershell %}
C:\> Invoke-Expression (Get-Content .\badscript.ps1)
{% endhighlight %}

![]({{ site.baseurl }}/assets/2016-01-21-12_06_29-CDC1-on-172.16.10.240.png)

This is one of just many, many, ways to bypass the execution policy. So if you think this is all you need to do to protect your system from nefarious operators, now's the time to rethink that. Execution Policy serves many good purposes, but stopping users of a computer from running commands is not one of them.
