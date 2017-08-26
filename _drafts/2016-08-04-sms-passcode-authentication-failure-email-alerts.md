---
layout: post
title: SMS PASSCODE Authentication Failure Email Alerts
date: 2016-08-04 04:05:41.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- PowerShell
- Scripting
- Security
- SMS PASSCODE
meta:
  _yoast_wpseo_primary_category: '8'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  _yoast_wpseo_content_score: '30'
  dsq_thread_id: '5038683976'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
SMS PASSCODE is a good tool, but it does not provide functionality to alert in the event of an authentication failure. It does, however, log quite verbosely to Windows event logs. Built-in Windows functionality can be used to receive email alerts when a login fails:
<h1>Create Send-FailedLoginAlert.ps1 script</h1>
Copy the following to a known location (in this example, C:\Scripts\Send-FailedLoginAlert.ps1).
https://gist.github.com/chrisbrownie/31f6784174452c5d64a98d04497336cc
<h1>Create the Scheduled Task</h1>
Create a scheduled task, configured as follows:
<ul>
<li>General</li>
<ul>
<li>Run whether user is logged on or not</li>
<li>Do not store password</li>
</ul>
<li>Triggers</li>
<ul>
<li>On an event:</li>
<ul>
<li>Log: SMS PASSCODE Security</li>
<li>Source: Authentication Proxy</li>
<li>Event ID: 2000</li>
</ul>
</ul>
<li>Actions</li>
<ul>
<li>Start a program</li>
<ul>
<li>Program: powershell.exe</li>
<li>Arguments: -ExecutionPolicy Unrestricted -File C:\scripts\Send-FailedLoginAlert.ps1</li>
</ul>
</ul>
</ul>
Now, any time event 2000 is fired in the *SMS PASSCODE Security* log, an email will be trigged using the parameters in the script.
