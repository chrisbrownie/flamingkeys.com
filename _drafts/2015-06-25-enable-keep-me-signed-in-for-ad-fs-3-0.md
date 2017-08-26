---
layout: post
title: Enable "Keep Me Signed In" for AD FS 3.0
date: 2015-06-25 03:14:14.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD FS
- Microsoft
- Office 365
- PowerShell
- Server 2012 R2
meta:
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3876817408'
  amazonS3_cache: a:3:{s:137:"//flamingkeys.com/wp-content/uploads/2015/06/2015-06-25-13_02_49-MTDC1-MTDC1.margiestravel.com-Remote-Desktop-Connection-Manager-v2.7.png";i:1017;s:76:"//flamingkeys.com/wp-content/uploads/2015/06/2015-06-25-13_08_39-Sign-In.png";i:1018;s:76:"//flamingkeys.com/wp-content/uploads/2015/06/2015-06-25-13_08_59-Sign-In.png";i:1019;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Keep Me Signed In (KMSI) is popularly used around the web-based software world to provide users with a login assurance that persists beyond the current session. In AD FS land, Microsoft call this <a href="https://technet.microsoft.com/en-us/library/mt148493.aspx" target="_blank">Persistent SSO</a>. Persistent SSO encapsulates a number of technologies, but the simplest of these is KMSI. KMSI will provide a user with a 24-hour cookie, allowing for logins to persist across browser sessions for up to a day.
Enable the KMSI checkbox with the following simple command on your Primary AD FS server:
{% highlight powershell %}
Set-AdfsProperties -EnableKmsi:$true
{% endhighlight %}
Easy as that! Run this command and your AD FS login page will update and look something like this:
<img class="aligncenter size-full wp-image-1017" src="{{ site.baseurl }}/assets/2015-06-25-13_02_49-MTDC1-MTDC1.margiestravel.com-Remote-Desktop-Connection-Manager-v2.7.png" alt="ADFSKMSI" width="371" height="321" />
If you start poking through cookies, you'll see the validity of the sign-in token change. With KMSI *off*, the cookie is only valid for this session:
<img class="aligncenter size-full wp-image-1018" src="{{ site.baseurl }}/assets/2015-06-25-13_08_39-Sign-In.png" alt="ADFS3KMSIOff" width="527" height="66" />
With KMSI *on*, the cookie is valid for 24 hours from the second it is provided to me:
<img class="aligncenter size-full wp-image-1019" src="{{ site.baseurl }}/assets/2015-06-25-13_08_59-Sign-In.png" alt="ADFS3KMSIOn" width="572" height="65" />
&nbsp;
