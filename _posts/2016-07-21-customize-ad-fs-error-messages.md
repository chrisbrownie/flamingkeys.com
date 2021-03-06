---
layout: post
title: How to Customize AD FS Error Messages
date: 2016-07-21 14:00:35.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD FS
- Microsoft
- Office 365
- windows
meta:
  _yoast_wpseo_content_score: '60'
  _edit_last: '1'
  _yoast_wpseo_primary_category: '8'
  _syntaxhighlighter_encoded: '1'
  _yoast_wpseo_focuskw_text_input: error message
  _yoast_wpseo_focuskw: error message
  _yoast_wpseo_metadesc: Entering a username incorrectly in AD FS results in a reasonably
    useful error message. Changing this message to suit your business is easy.
  _yoast_wpseo_linkdex: '73'
  dsq_thread_id: '5003060783'
  _thumbnail_id: '1142'
  amazonS3_cache: a:4:{s:70:"//flamingkeys.com/wp-content/uploads/2016/07/adfs-invalid-username.png";i:1140;s:80:"//flamingkeys.com/wp-content/uploads/2016/07/adfs-invalid-username-new-error.png";i:1142;s:78:"//flamingkeys.com/wp-content/uploads/2016/07/adfs-invalid-username-300x126.png";i:1140;s:88:"//flamingkeys.com/wp-content/uploads/2016/07/adfs-invalid-username-new-error-300x130.png";i:1142;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/07/customize-ad-fs-error-messages/
---

![]({{ site.baseurl }}/assets/adfs-invalid-username.png)

Entering a username incorrectly in AD FS results in a reasonably useful error message. However, [some folks](https://social.technet.microsoft.com/Forums/windowsserver/en-US/bd846a1a-a4c1-4bfa-a2ec-effc2c8bc33e/one-more-customized-logon-message-question?forum=ADFS) desire to change this, which is perfectly OK too. 

![]({{ site.baseurl }}/assets/adfs-invalid-username-new-error.png)

Poking through the HTML behind `IdpInitiatedSignon.aspx` (the page that is rendered for forms-based authentication to AD FS) shows that the error messages for a) invalid username format, and b) empty password, are both stored in a JavaScript function called `LoginErrors()`.

{% highlight javascript %}
function LoginErrors(){
    this.userNameFormatError = 'Enter your user ID in the format \u0026quot;domain\\user\u0026quot; or \u0026quot;user@domain\u0026quot;.';
    this.passwordEmpty = 'Enter your password.';
}
{% endhighlight %}

Fortunately, JavaScript provides great native functionality for overriding inbuilt functions, so we can simply redefine `LoginErrors` later on. The page will then utilise that in the event of either condition (username format error or empty password) being met. Be sure to follow the approach below that matches your environment:

## Default AD FS theme (Create custom theme)

If you don't already have a custom AD FS theme, why not? They're a great way to customise the (somewhat bland) default AD FS interface. Let's create one now! You can use this to apply the customisations here, as well as to update countless other display and functionality features of the AD FS interface.

{% highlight powershell %}
New-AdfsWebTheme -Name customtheme -SourceName default
{% endhighlight %}

Once you've created your custom theme, follow the steps below.

## Existing custom AD FS theme

If you already have a custom AD FS theme, you'll want to perform the following steps:
1. Download your custom theme (herein 'customtheme') to your local machine
  {% highlight powershell %}
  Export-AdfsWebTheme -name customtheme -directoryPath C:\adfs\customtheme
  {% endhighlight %}

2. Add the following code to the bottom of the onload.js file, modifying error messages as appropriate:
  {% highlight powershell %}
  function LoginErrors() {
      this.userNameFormatError = 'Please enter your E-mail address.';
      this.passwordEmpty = 'Enter your password.';
  }
  {% endhighlight %}

3. Upload the customised onload.js file to your custom theme:
  {% highlight powershell %}
  Set-AdfsWebTheme -TargetName customtheme -AdditionalFileResource @{Uri=’/adfs/portal/script/onload.js’;path="c:\adfs\customtheme\script\onload.js"}
  {% endhighlight %}

4. Apply the custom theme:
  {% highlight powershell %}
  Set-AdfsWebConfig -ActiveThemeName customtheme
  {% endhighlight %}
