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
redirect_from:
  - /2016/08/sms-passcode-authentication-failure-email-alerts/
---
SMS PASSCODE is a good tool, but it does not provide functionality to alert in the event of an authentication failure. It does, however, log quite verbosely to Windows event logs. Built-in Windows functionality can be used to receive email alerts when a login fails:

# Create Send-FailedLoginAlert.ps1 script

Copy the following to a known location (in this example, `C:\Scripts\Send-FailedLoginAlert.ps1`).

```powershell
# Fired when a SMSSec 2000 (AuthN failure) occurs
 
$SmtpDetails = @{
    "SmtpServer" = "smtp.margiestravel.com"
    "To" = "admin@margiestravel.com"
    "Subject" = ""
    "Body" = ""
    "From" = "alerts@margiestravel.com"
    "BodyAsHtml" = $true
    "Priority" = "High"
    }
 
# Get the latest 2000 event from the SMSSec log
$Event = Get-EventLog -LogName "SMSSec" -Newest 1 -InstanceId 2000
 
# Tear the details of the event apart into a hashtable we can work with
$EventDetails = @{}
$event.ReplacementStrings.Split("`n") | % {
    try { $EventDetails.Add($_.Split(":")[0].Trim(), $_.split(":")[1].Trim()) } catch { }
    }
     
$SmtpDetails.Subject = "'$($EventDetails.Login)': SMS PASSCODE Login Failure! "
$SmtpDetails.Body = @"
<strong>SMS PASSCODE Authentication Failure!</strong><br /><br />
Timestamp: $($Event.TimeGenerated)<br />
Username: $($EventDetails.Login)<br />
End-User IP: $($EventDetails.'End-user IP')<br />
Reason: $($EventDetails.Reason)<br /><br />
Session ID: $($EventDetails.'Session ID')<br />
"@
 
Send-MailMessage @SmtpDetails
```

# Create the Scheduled Task

Create a scheduled task, configured as follows:

* General
  * Run whether user is logged on or not
  * Do not store password
* Triggers
  * On an event:
    * Log: `SMS PASSCODE Security`
    * Source: `Authentication Proxy`
    * Event ID: `2000`
* Actions
  * Start a program
    * Program: `powershell.exe`
    * Arguments: `-ExecutionPolicy Unrestricted -File C:\scripts\Send-FailedLoginAlert.ps1`

Now, any time event `2000` is fired in the `SMS PASSCODE Security` log, an email will be trigged using the parameters in the script.
