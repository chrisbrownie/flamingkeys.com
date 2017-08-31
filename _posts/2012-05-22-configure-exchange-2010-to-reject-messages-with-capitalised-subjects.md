---
layout: post
title: Configure Exchange 2010 to Reject messages with Capitalised Subjects
date: 2012-05-22 13:00:00.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Email
- Exch
- Exchange
- PowerShell
- tips
meta:
  dsq_thread_id: '699178102'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Upon receiving a few emails with their subjects entirely in capital letters, I wondered if I could defuse my extreme frustration at this situation using every Exchange admin’s best friend; the Transport Rule.

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/05/image3.png)

I was able to create the following transport rule to bounce/reject all messages with only capital letters in the subject, including ignoring the common indicators for replies etc.:

{% highlight powershell %}
New-TransportRule -Name 'All Caps is Bad' -SubjectOrBodyMatchesPatterns '^(R[eE]: )*[^a-z]*$','^(F[wW]: )*[^a-z]*$','^(Fwd: )*[^a-z]*$'
{% endhighlight %}

This creates a new transport rule, and any message whose subject (or body) matches any of the following Regular Expressions will be bounced:
* `^(R[eE]: )*[^a-z]*$` (eg: "Re: THIS IS IMPORTANT" or "THIS IS IMPORTANT")
* `^(F[wW]: )*[^a-z]*$` (eg: "FW: THIS IS IMPORTANT")
* `^(Fwd: )*[^a-z]*$` (eg: "Fwd: THIS IS IMPORTANT")

When a user tries to send a message with only capitals in the title, the following is returned. Note the 5.7.111 "Do not use a capitalised" subject bounce message.

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/05/image4.png)

This is obviously not something I recommend deploying in production, but it does show how easy it is to evaluate message bodies and subjects using regular expressions.
