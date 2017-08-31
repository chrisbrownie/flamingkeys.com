---
layout: post
title: Start-Transcript with IncludeInvocationHeader
date: 2015-02-05 22:10:04.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Automation
- Microsoft
- PowerShell
- Scripting
- tips
- VNext
- windows
- Windows 10
- Windows Preview
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  _edit_last: '1'
  dsq_thread_id: '3489315842'
  amazonS3_cache: a:3:{s:149:"//flamingkeys.com/wp-content/uploads/2015/02/2015-02-06-09_07_32-StartTranscriptCommand.IncludeInvocationHeader-Property-Microsoft.PowerShell.Co_.png";i:899;s:157:"//flamingkeys.com/wp-content/uploads/2015/02/2015-02-06-09_07_32-StartTranscriptCommand.IncludeInvocationHeader-Property-Microsoft.PowerShell.Co_-300x154.png";i:899;s:157:"//flamingkeys.com/wp-content/uploads/2015/02/2015-02-06-09_07_32-StartTranscriptCommand.IncludeInvocationHeader-Property-Microsoft.PowerShell.Co_-730x375.png";i:899;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/02/start-transcript-with-includeinvocationheader/
---
While tinkering with PowerShell in the new build 9926 of Windows 10, I've investigating using `Start-Transcript` more in my work. This is a great way of logging the inputs and outputs of PowerShell consoles. The cmdlet basically says what it does on the box - transcripts the whole console output.

In the new build of Windows, there's an additional (as yet [undocumented](https://msdn.microsoft.com/en-us/library/microsoft.powershell.commands.starttranscriptcommand.includeinvocationheader(v=vs.85).aspx)) switch parameter for this cmdlet: `-IncludeInvocationHeader`.

![]({{ site.cdnbaseurl }}wp-content/uploads/2015/02/2015-02-06-09_07_32-StartTranscriptCommand.IncludeInvocationHeader-Property-Microsoft.PowerShell.Co_.png)

If you append this switch when running `Start-Transcript`, you'll see the outputted text file differs ever so slightly. Before each entered command, the transcript function prepends a timestamp `Command start time: yyyyMMddhhmmss`. This is a great method for always timing command execution should you wish to come back at a later stage and refer to its performance.

    **********************
    Command start time: 20150206080056
    **********************
    PS C:\test> gci
        Directory: C:\test
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        6/02/2015     07:59                A
    d-----        6/02/2015     07:59                B
    d-----        6/02/2015     07:59                C
    -a----        6/02/2015     07:59           1147 1.txt
    -a----        6/02/2015     08:00            783 2.txt

Not a monumental change, by any standards, but it's certainly a handy one the PowerShell team will (possibly, maybe, potentially, or possibly not) include in Windows 10 and the production release of Windows PowerShell 5!
