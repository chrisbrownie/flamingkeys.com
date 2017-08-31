---
layout: post
title: How to monitor Exchange 2010 mailbox moves real-time
date: 2011-03-25 11:07:35.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Exchange
- PowerShell
- Scripting
- Server 2008 R2
- tips
meta:
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: http://www.flamingkeys.com/2011/03/how-to-monitor-exchange-2010-mailbox-moves-real-time/?utm_campaign=twitter&utm_medium=twitter&utm_source=twitter
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '262458537'
  _wp_old_slug: ''
  _sexybookmarks_shortUrl: http://fla.im/ee8cnN
  _sexybookmarks_permaHash: 40dded92f53a5c5a1aa4028fa6330e83
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/03/how-to-monitor-exchange-2010-mailbox-moves-real-time/
---

I am in the middle of a large Exchange 2003 to Exchange 2010 migration. Moving a few large mailboxes at a time can be a time-consuming thing, especially when I don't know if the moves are complete. This little snippet allows me to keep an eye on a console window and see when various moves are complete.

This script will keep completed moves in the display:
{% highlight powershell %}
while ($true) { Get-MoveRequest | Get-MoveRequestStatistics; Start-Sleep -Seconds 2; Clear-Host; }
{% endhighlight %}

This script will hide completed moves from the display:
{% highlight powershell %}
while ($true) { Get-MoveRequest | Get-MoveRequestStatistics | Where {$_.Status -ne "Completed"} ; Start-Sleep -Seconds 2; Clear-Host; }
{% endhighlight %}

This script will only show completed moves:

{% highlight powershell %}
while (1 -eq 1) { Get-MoveRequest | Get-MoveRequestStatistics | Where {$_.Status -eq "Completed"} ; Start-Sleep -Seconds 2; Clear-Host; }
{% endhighlight %}

Not an overly in-depth blog post, but will probably come in handy for someone. This could also be expanded to email upon move completion or failure, if there's enough interest I'll write that up too.
