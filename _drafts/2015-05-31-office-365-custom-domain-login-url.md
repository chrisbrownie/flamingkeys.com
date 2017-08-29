---
layout: post
title: Office 365 Custom Domain Login URL
date: 2015-05-31 23:59:21.000000000 +10:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  _edit_last: '1'
  dsq_thread_id: '3810450426'
  amazonS3_cache: a:2:{s:56:"//flamingkeys.com/wp-content/uploads/2015/05/readify.png";i:982;s:61:"//flamingkeys.com/wp-content/uploads/2015/05/logindefault.png";i:983;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Consider a scenario where you've configured custom branding for your Windows Azure Active Directory login URL at https://login.microsoftonline.com. The custom branding doesn't show up until a user's entered their username and hit tab, and then the images have loaded, by which point they're probably most of the way through typing their password anyway. If you want to deploy links on an intranet or to browser favourites or anything like that to send users directly to your Office 365 custom branding, you'll likely want to have that custom branding in place from the get-go. I struggled a little to find this, but eventually found application guidance here on the <a href="http://blogs.technet.com/b/ad/archive/2015/02/11/how-to-use-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app.aspx" target="_blank">TechNet Active Directory Team Blog</a>.
Basically, you want to link your end users to:
<pre>https://login.microsoftonline.com/?whr=mycustomdomain.com</pre>
After some quick brand-guessing, I found out that local development shop, Readify, have taken the approach of customising WAAD, so here's a great way to see the link in action: <a href="https://login.microsoftonline.com/?whr=readify.com.au" target="_blank">https://login.microsoftonline.com/?whr=readify.com.au</a>.You can also use this mechanism to redirect people to AD FS, as Microsoft have done for their corporate tenant: <a href="https://login.microsoftonline.com/?whr=microsoft.com" target="_blank">https://login.microsoftonline.com/?whr=microsoft.com</a>.
Give this a crack, hopefully it helps keep your marketing people happy, and your users won't have to see much more of that awful highway Microsoft usually show us at the sign-in page.
In case you haven't yet customised your Azure AD - <a href="https://msdn.microsoft.com/en-us/library/azure/dn532270.aspx" target="_blank">what are you waiting for</a>?
Hot: <a href="https://flamingkeys.com/wp-content/uploads/2015/05/readify.png"><img class="aligncenter size-full wp-image-982" src="{{ site.baseurl }}/assets/readify.png" alt="readify" width="1221" height="710" /></a>
Not:
<a href="https://flamingkeys.com/wp-content/uploads/2015/05/logindefault.png"><img class="aligncenter size-full wp-image-983" src="{{ site.baseurl }}/assets/logindefault.png" alt="logindefault" width="1221" height="710" /></a>