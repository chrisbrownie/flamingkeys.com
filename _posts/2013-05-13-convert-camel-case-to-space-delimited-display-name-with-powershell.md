---
layout: post
title: Convert Camel Case to space delimited Display Name with PowerShell
date: 2013-05-13 10:46:47.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- PowerShell
- Scripting
- Text
- tips
meta:
  dsq_thread_id: '1284190829'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

I’m writing a very rudimentary script that has a need to convert something like “groundFloorMeetingRoom” to “Ground Floor Meeting Room”. Rather than prompt the user to enter this, I decided it’d be easier to just get the script to do it. This turned out to be a fun little exercise, resulting in the *CamelCaseToDisplayName *function below. Hopefully it can help someone!

{% highlight powershell %}
function CamelCaseToDisplayName ([string]$inString) {
  $newString = ""
  $stringChars = $inString.GetEnumerator()
  $charIndex = 0
  foreach ($char in $stringChars) {
    # If upper and not first character, add a space
    if ([char]::IsUpper($char) -eq "True" -and $charIndex -gt 0) {
      $newString = $newString + " " + $char.ToString()
    } elseif ($charIndex -eq 0) {
      # If the first character, make it a capital always
      $newString = $newString + $char.ToString().ToUpper()
    } else {
      $newString = $newString + $char.ToString()
    }
    $charIndex++
  }
  $newString
}
{% endhighlight %}

It’s pretty simple to run, just throw something like this in your script:

`$newName = CamelCaseToDisplayName “groundFloorMeetingRoom”`
