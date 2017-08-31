---
layout: post
title: Send Bulk Test Emails with PowerShell
date: 2011-08-11 14:02:47.000000000 +10:00
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
  _wp_jd_target: ''
  _wp_jd_bitly: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _edit_last: '1'
  robotsmeta: index,follow
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '382859980'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/08/send-bulk-test-emails-with-powershell/
---
I'm doing some load testing on one of my SMTP Smart Hosts, and needed to send a few thousand emails in a controlled space of time. I've written this quick little script to do so:
{% highlight powershell %}
$scriptSettings = @{
  NumberOfEmails = 100
  TimeBetweenEmails = 1 #in seconds
  MailDestination = 'kim.akers@contoso.com'
  MailSubject = 'Test'
  MailServer = 'mail.contoso.com'
  MailFrom = 'no-reply@contoso.com'
}
for ($i=1; $i -le $scriptSettings.NumberOfEmails; $i++) {
  try { 
    Send-MailMessage `
      -To $scriptSettings.mailDestination `
      -Subject $scriptSettings.mailSubject `
      -SmtpServer $scriptSettings.mailServer `
      -From $scriptSettings.From `
      -Body "Email number $i"
  } catch { 
    "Error sending email $i" 
  }
  Start-Sleep -Seconds $scriptSettings.TimeBetweenEmails
}
{% endhighlight %}
