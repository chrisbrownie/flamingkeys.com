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
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/05/image3.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb3.png" width="390" height="62" /></a>
I was able to create the following transport rule to bounce/reject all messages with only capital letters in the subject, including ignoring the common indicators for replies etc.:
{% highlight powershell %}New-TransportRule -Name 'All Caps is Bad' -SubjectOrBodyMatchesPatterns '^(R[eE]: )*[^a-z]*$','^(F[wW]: )*[^a-z]*$','^(Fwd: )*[^a-z]*$'{% endhighlight %}
This creates a new transport rule, and any message whose subject (or body) matches any of the following Regular Expressions will be bounced:
<ul>
<li>^(R[eE]: )*[^a-z]*$ (eg: “Re: THIS IS IMPORTANT” or “THIS IS IMPORTANT”) </li>
<li>^(F[wW]: )*[^a-z]*$ (eg: “FW: THIS IS IMPORTANT”) </li>
<li>^(Fwd: )*[^a-z]*$ (eg: “Fwd: THIS IS IMPORTANT”) </li>
</ul>
When a user tries to send a message with only capitals in the title, the following is returned. Note the 5.7.111 “Do not use a capitalised” subject bounce message.
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/05/image4.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px; padding-top: 0px" title="image" border="0" alt="image" src="{{ site.baseurl }}/assets/image_thumb4.png" width="392" height="348" /></a>
&#160;
This is obviously not something I recommend deploying in production, but it does show how easy it is to evaluate message bodies and subjects using regular expressions.
