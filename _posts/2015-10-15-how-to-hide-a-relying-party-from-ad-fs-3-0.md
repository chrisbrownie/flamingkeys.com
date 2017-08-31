---
layout: post
title: How to Hide a Relying Party from AD FS 3.0
date: 2015-10-15 13:01:41.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD FS
- Computers
- Microsoft
- PowerShell
- Scripting
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  dsq_thread_id: '4227374423'
  amazonS3_cache: a:4:{s:91:"//flamingkeys.com/wp-content/uploads/2015/10/2015-10-12-21_34_21-Greenshot-capture-form.png";i:1052;s:94:"//flamingkeys.com/wp-content/uploads/2015/10/2015-10-12-21_51_03-MT-STS01-on-172.16.10.240.png";i:1057;s:99:"//flamingkeys.com/wp-content/uploads/2015/10/2015-10-12-21_34_21-Greenshot-capture-form-300x115.png";i:1052;s:102:"//flamingkeys.com/wp-content/uploads/2015/10/2015-10-12-21_51_03-MT-STS01-on-172.16.10.240-300x109.png";i:1057;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/10/how-to-hide-a-relying-party-from-ad-fs-3-0/
---
If you've set up AD FS before, you've probably seen this drop-down list that allows your users to select an application to log into. You may also have a particularly troublesome app that doesn't work with this IdP initiated method of login. Using the steps below, we can hide one or more of the options from the AD FS 3.0 dropdown list.

![]({{ site.baseurl }}/assets/2015-10-12-21_34_21-Greenshot-capture-form.png)

Using the theming capabilities of AD FS 3.0, we can introduce some JavaScript that removes applications from the list as the page is loaded.
I am *not* a web guy by any means, so my JavaScript isn't great...but it works. Happy to accept corrections from anyone better than this at me.

<aside class="aside-info">Update, 3 Nov 2015: Like clockwork, someone's come up with a better option! Thanks to ![Andrew in the comments](/how-to-hide-a-relying-party-from-ad-fs-3-0/#comment-2339319804) for his great solution, it's a lot more slick than my initial approach.</aside>

1. Create a custom theme:
  {% highlight powershell %}
  New-AdfsWebTheme -Name MyCustomTheme -SourceName default
  {% endhighlight %}

2. Download the custom theme
  {% highlight powershell %}
  New-Item -Type Directory C:\adfs\MyCustomTheme
  Export-AdfsWebTheme -Name default -DirectoryPath C:\adfs\myCustomTheme
  {% endhighlight %}

3. Add the following to line 5 of the `C:\adfs\MyCustomTheme\script\onload.js` file:
  {% highlight javascript %}
  var dropDownList = document.getElementById('idp_RelyingPartyDropDownList');
  var itemsToRemove = ['Contoso App2', 'Contoso App3'];
  // if we found the dropdown
  if (dropDownList) {
    // Run through every item flagged for removal
    for (var i=0; i < itemsToRemove.length; i++ ) {
      // Recurse through each item in the dropdown
      for (var j=0; j < dropDownList.length; j++ ) {
        // check if the item matches
        if ( dropDownList.options[j].text == itemsToRemove[i] ) {
          dropDownList.remove(j);
        }
      }
    }
  }
  {% endhighlight %}

4. Upload the custom theme to AD FS:
  {% highlight powershell %}
  Set-AdfsWebTheme -TargetName MyCustomTheme -AdditionalFileResource @{Uri='/adfs/portal/script/onload.js';path="C:\adfs\MyCustomTheme\script\onload.js"}
  {% endhighlight %}

5. Then activate the custom theme:
  {% highlight powershell %}
  Set-AdfsWebConfig -ActiveThemeName MyCustomTheme
  {% endhighlight %}

Easy as! Now when users load the page, they won't see the two apps defined above:
![]({{ site.baseurl }}/assets/2015-10-12-21_51_03-MT-STS01-on-172.16.10.240.png)
