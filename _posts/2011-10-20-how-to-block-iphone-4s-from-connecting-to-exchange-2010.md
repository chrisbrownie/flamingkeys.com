---
layout: post
title: How to Block iPhone 4S from Connecting to Exchange 2010
date: 2011-10-20 11:36:29.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Apple
- Automation
- Exchange
- Hacks
- iPhone
- iPhone 4S
- Scripting
- Security
- Siri
- tips
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
  _edit_last: '1'
  robotsmeta: index,follow
  Hide SexyBookmarks: '0'
  Hide OgTags: '0'
  dsq_thread_id: '448216036'
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
excerpt: With the inherent flaws (AKA features) offered by Apple’s new iPhone 4S with
  Siri, you may be required to block it from accessing your Exchange server via ActiveSync..
  Here's how!
redirect_from:
  - /2011/10/how-to-block-iphone-4s-from-connecting-to-exchange-2010/
---

## Siri, Tell Me About The Problem

With the [inherent](http://www.theage.com.au/it-pro/security-it/iphone-4s-security-hole-uncovered-20111019-1m6xt.html) [flaws](http://nakedsecurity.sophos.com/2011/10/19/siri-iphone-4s-unlocked/) (AKA features) offered by Apple’s new iPhone 4S with [Siri](http://www.apple.com/iphone/features/siri.html), I was asked to block access to our ActiveSync services from 4S devices. Unfortunately, Exchange’s Device Access Rules don’t allow us to drill any further down than "iPhone" (Honestly I think this is the fault of Apple for not reporting device model correctly...but that’s just me).

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image.png)

In my environment (and most others, no doubt), "Sorry, Exchange can’t do that" isn’t really good enough, so extra troubleshooting was required. I decided to jump into Exchange Management Shell (EMS) and check out any more properties I could find. Issuing the `Get-ActiveSyncDevice | Out-Gridview` Exchange command yielded some useful results (some details removed from the graphic to protect the guilty). I’ve included all the properties that contain any information of use in blocking the devices. The property of particular interest is `DeviceUserAgent`. This seems to show some consistency, in that all iPhones are in the format "Apple-iPhone*<span style="color: #f00;">x</span>*C1". "<span style="color: #f00;">x</span>" seems to be an incremented number that increases with device revisions.

![]({{ site.cdnbaseurl }}/wp-content/uploads/2011/10/image1.png)


By doing some user to model to Operating System spot-checks (and thanks to some helpful people on Twitter!) I was able to ascertain that `DeviceUserAgent` directly correlates to the hardware of the device connecting to Exchange (not necessarily the version of the iOS build, as was my initial fear). The `DeviceUserAgent` for the iPhone 4 is `Apple-iPhone3C1`, and the iPhone 4S is `Apple-iPhone4C1`. Knowing this, I can now update my filter to show only iPhone 4S devices (that is, where the `DeviceUserAgent` begins with `Apple-iPhone4C1`:

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image2.png)

It appears that the string following the slash (`/`) in the `DeviceUserAgent` may relate to the iOS build on the device, but this isn’t really relevant at this stage. If Apple start to offer Siri (and this security hole) on previous versions of the hardware, I may need to adjust the queries to suit.

## TLDR; Siri, Just Tell Me How to Nuke You!

Dependent on your organisational policies, you may be able to take the [easy way out](http://support.apple.com/kb/DL1465) in blocking Siri on the iPhone 4S. We run a [BYOD](http://www.zdnet.com/debate/great-debate-bring-your-own-device/6313019) environment for most staff who want to, and we take a completely hands-off approach on this (no support, no assistance beyond "these are your settings"). For this reason, I’ve decided the easiest approach (since Device Access Rules aren’t going to work for me here) is to write a script that simply removes any connections to these  devices every time it runs. I’ll leave the frequency up to you to decide. The one line script looks like this:

{% highlight powershell %}
Get-ActiveSyncDevice –filter { DeviceUserAgent –like "Apple-iPhone4C1*"} | Remove-ActiveSyncDevice –Confirm:$false
{% endhighlight %}

What does this do?

| Command Segment | Description
| --- | ---
| `Get-ActiveSyncDevice` | Get an array of all the ActiveSync Devices
| `-filter { DeviceUserAgent –like "Apple-iPhone4C1*" }` | Only return devices whose DeviceUserAgent begins with `Apple-iPhone4C1/`
| Remove-ActiveSyncDevice | Remove the devices
| -confirm:$false | Don’t prompt for confirmation (we want this to be automated!)

<aside class="aside-error">Keep in mind any typos here could be devastating, so make sure you only add the `–confirm:$false` argument once you’ve tested and confirmed this works for you.</aside>

After running this script, I sat content that iPhone 4S devices would would be blocked from my Exchange environment. However it occurred to me over lunch, whilst eating my quiche, that devices could simply re-join themselves to Exchange! On to plan B (Well.. Plan A, part 2):

Within IIS, it’s a relatively trivial task to block host headers. On your CAS servers, ensure you have the role feature ‘Request Filtering’ installed for IIS. This will allow you to do some very cool filtering dependent on a number of parameters. (Edit: Belated hat tip to [@jamesbannan](http://twitter.com/jamesbannan) for the IIS idea)

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image3.png)

Jump into IIS Manager and drill down as far as you’d like. I did this at the server level; I don’t want 4S devices accessing anything! You could easily do this on just your OWA site, or even further down the tree.
Under *IIS*, select *Request Filtering*. On the *Rules* tab, select *Add Filtering Rule*. 

Enter a descriptive name, then under *Scan Headers* enter a new line `User-Agent`. This will tell IIS to check the "User-Agent" component of all request headers. Leave "Applies To" empty, so it will apply to all file types. Under "Deny Strings", enter the user agent of the iPhone 4S (`Apple-iPhone4C1*`). You could theoretically block any user agents you want to here . My rule looks like this:

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image4.png)

When browsing from a device with the host header specified above (i.e. the iPhone 4S), IIS will return this beautiful error message:

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image5.png)

When browsing from an allowed device (say, an iPhone 4):

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/10/image6.png)

Hopefully Apple will remedy this flaw soon so that we can stop with this workaround. It’s irresponsible of a software manufacturer to release such a hole...but that’s an argument for another blog post by someone much smarter than me.
