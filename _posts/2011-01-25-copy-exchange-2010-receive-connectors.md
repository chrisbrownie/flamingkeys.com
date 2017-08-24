---
layout: post
title: Copy Exchange 2010 Receive Connectors
date: 2011-01-25 10:04:53.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Email
- Exchange
- PowerShell
- Scripting
- tips
meta:
  _sexybookmarks_shortUrl: http://fla.im/ewow6E
  _sexybookmarks_permaHash: 246f4e5a3bcec58123dc403112b6105d
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '215528107'
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_bitly: ''
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _syntaxhighlighter_encoded: '1'
  Hide SexyBookmarks: '0'
  Hide OgTags: '0'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

In deploying a new Exchange 2010 server I found myself needing to duplicate a receive connector with about 20 different IP entries. Being lazy (or as Terence called it; efficient), I started googling and found [this post](http://terenceluk.blogspot.com/2010/11/how-do-i-exportimport-exchange-20072010.html) by Terence Luk. His procedure works, but I figured there had to be a faster way. After a few minutes of fiddling I managed to compress his technique into this one-liner:

{% highlight powershell %}
New-ReceiveConnector "Mail from Printers" -Server EX02 -Bindings 0.0.0.0:25 -RemoteIPRanges ( Get-ReceiveConnector "EX01\Mail from Printers" ).RemoteIPRanges
{% endhighlight %}

This one does the same as [Terence](http://terenceluk.blogspot.com/)'s process, but without the intervention in the middle. I hope this helps aspiring lazy admins like me.
