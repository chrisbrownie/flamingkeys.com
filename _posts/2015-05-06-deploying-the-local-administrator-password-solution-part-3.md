---
layout: post
title: Deploying the Local Administrator Password Solution Part 3
date: 2015-05-06 13:11:21.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD-DS
- Automation
- Computers
- Group Policy
- Microsoft
- PowerShell
- Scripting
- Security
- Server 2012 R2
- windows
meta:
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3741222046'
  amazonS3_cache: a:3:{s:53:"//flamingkeys.com/wp-content/uploads/2015/05/GP-1.png";i:967;s:60:"//flamingkeys.com/wp-content/uploads/2015/05/LAPSUsage-1.png";i:968;s:60:"//flamingkeys.com/wp-content/uploads/2015/05/LAPSUsage-2.png";i:969;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
In this series of three posts, I demonstrate the installation and configuration of Microsoft's Local Administrator Password Solution (LAPS). This is the third and final post that covers the group policy configuration of LAPS, as well as a brief demonstration. For links to parts 1 and 2, see the bottom of this post.

# Configuring Group Policy

We've got the software installed, and AD configured, all that's left is to turn the thing on! Fortunately there's an ADM file included with the management tools, so this is pretty straightforward. Open GPMC and edit the LAPS policy we created earlier. If you'd prefer, you can create a separate policy for configuration. Either way, open Group Policy Editor and navigate to Policies -> Administrative Templates -> LAPS. ![GP-1]({{ site.baseurl }}/assets/GP-1.png) These settings are pretty straightforward, so configure them how you like. I've configured mine like so:

*   Enable local admin password management: **Enabled**
*   Password Settings: **Enabled**
    *   Complexity: Large letters, small letters, numbers, specials
    *   Length: 14 characters
    *   Age: 30 days
*   Name of administrator account to manage: **Not Configured** (this will use the default SID -500 account)
*   Do not allow password expiration time longer than required by policy: **Enabled**

Congratulations, you're done! It's as easy as that!

# Using LAPS

There's two ways to retrieve a computer's password from Active Directory. ## The Hard Way Every computer updates its AD attributes, so surely Attribute Editor would be a good place to start? Crank up Users and Computers and browse to the computer. You'll see the admin password and (unintelligible) password expiry time: ![LAPSUsage-1]({{ site.baseurl }}/assets/LAPSUsage-1.png) ## The Easy Way Remember right back in part 1 we had the option to install a fat client? Now's the time to check that out. You'll see an app called **LAPS UI** pinned to your start screen. Run it as administrator and you'll see a fairly basic user interface. Enter a computer's name and hit the *Search* button to retrieve the computer's password and a readable expiry time. ![LAPSUsage-2]({{ site.baseurl }}/assets/LAPSUsage-2.png)

As an aside, [/u/accountnumber3](https://www.reddit.com/user/accountnumber3) on [reddit](https://www.reddit.com/r/sysadmin/comments/356337/howto_deploying_the_local_administrator_password/cr1erf5) offers this programmatic solution to retrieving the password. This would be very useful if you wish to retrieve these values using a script or other unattended solution:

{% highlight powershell %}
Get-ADComputer MTCL1 -Properties ms-Mcs-AdmPwd | select name, ms-Mcs-AdmPwd
{% endhighlight %}

Well, that's that! There's not much more to do. If you want to add more OUs or security groups in future, just run through the relevant steps again. This is a really great tool that's very simple to deploy, and it can increase your AD security exponentially. Thanks for reading!

# What Else?

* [See part 1 for installing LAPS](https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-1/) 
* [See part 2 for configuring Active Directory](https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-2/)