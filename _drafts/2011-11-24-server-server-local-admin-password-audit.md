---
layout: post
title: Server Local Admin Password Audit
date: 2011-11-24 15:32:57.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Automation
- PowerShell
- Scripting
- Server 2003
- Server 2008
- Server 2008 R2
- tips
- WMI
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
  Hide SexyBookmarks: '0'
  Hide OgTags: '0'
  dsq_thread_id: '482086053'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
A situation arose where I had to check all our Windows servers to see if any were using a particular local administrator password. I wrote the following script to allow me to test a dynamically generated set of credentials. It uses WMI because that allows specifying a particular set of credentials and is relatively lightweight in comparison to other tests.
{% highlight powershell %}# Generate a secure string with the password stored in it. Alternatively you could prompt the user
$pass = ConvertTo-SecureString -AsPlainText -Force -String "Pa55w0rdToTest"
# What username should be tested?
$username = "Administrator"
# Find every computer in AD running an operating system with "Server" in its name.
foreach ($server in $(Get-ADComputer -Filter {OperatingSystem -like "*Server*"})) {
# Make sure the server can be contacted
if (Test-Connection $server.Name -Quiet) {
# Build a local administrator credential
$credential = New-Object System.Management.Automation.PSCredential("$($server.Name)\$username",$pass)
# Try to connect to the computer with the credential
try {
$null = Get-WmiObject Win32_OperatingSystem -Credential $credential -ComputerName $server.Name
Write-Host "$($server.Name) :: Success" -ForegroundColor Green -BackgroundColor Black
} catch { Write-Host "$($server.Name) :: Fail" -ForegroundColor Red -BackgroundColor Black }
}
}{% endhighlight %}
