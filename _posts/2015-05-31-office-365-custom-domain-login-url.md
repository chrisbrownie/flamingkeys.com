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
redirect_from:
  - /2015/05/office-365-custom-domain-login-url/
---

<aside class="aside-warn">Warning (August 2017): The guidance below is of questionable use now that Microsoft have started to roll out the new Azure AD sign-in experience which doesn't seem to honour the `?whr` querystring. YMMV!</aside>

Consider a scenario where you've configured custom branding for your Windows Azure Active Directory login URL at `https://login.microsoftonline.com`. The custom branding doesn't show up until a user's entered their username and hit tab, and then the images have loaded, by which point they're probably most of the way through typing their password anyway. If you want to deploy links on an intranet or to browser favourites or anything like that to send users directly to your Office 365 custom branding, you'll likely want to have that custom branding in place from the get-go. I struggled a little to find this, but eventually found application guidance here on the [TechNet Active Directory Team Blog](http://blogs.technet.com/b/ad/archive/2015/02/11/how-to-use-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app.aspx).

Basically, you want to link your end users to `https://login.microsoftonline.com/?whr=mycustomdomain.com`

After some quick brand-guessing, I found out that local development shop, Readify, have taken the approach of customising WAAD, so here's a great way to see the link in action: [https://login.microsoftonline.com/?whr=readify.com.au](https://login.microsoftonline.com/?whr=readify.com.au).You can also use this mechanism to redirect people to AD FS, as Microsoft have done for their corporate tenant: [https://login.microsoftonline.com/?whr=microsoft.com](https://login.microsoftonline.com/?whr=microsoft.com).

Give this a crack, hopefully it helps keep your marketing people happy, and your users won't have to see much more of that awful highway Microsoft usually show us at the sign-in page.

In case you haven't yet customised your Azure AD - [what are you waiting for](https://msdn.microsoft.com/en-us/library/azure/dn532270.aspx)?

Hot: 

![]({{ site.cdnbaseurl }}wp-content/uploads/2015/05/readify.png)

Not:

![]({{ site.cdnbaseurl }}wp-content/uploads/2015/05/logindefault.png)
