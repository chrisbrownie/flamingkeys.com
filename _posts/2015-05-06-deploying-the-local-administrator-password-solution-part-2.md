---
layout: post
title: Deploying the Local Administrator Password Solution Part 2
date: 2015-05-06 12:50:21.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD-DS
- Annoyances
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
  dsq_thread_id: '3741171085'
  amazonS3_cache: a:10:{s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-1.png";i:952;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-3.png";i:954;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-2.png";i:953;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-4.png";i:955;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-5.png";i:956;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-6.png";i:957;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-7.png";i:958;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-8.png";i:959;s:53:"//flamingkeys.com/wp-content/uploads/2015/05/AD-9.png";i:960;s:54:"//flamingkeys.com/wp-content/uploads/2015/05/AD-10.png";i:961;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/05/deploying-the-local-administrator-password-solution-part-2/
---
In this series of three posts, I demonstrate the installation and configuration of Microsoft's Local Administrator Password Solution (LAPS). This is the second post that covers configuration of Active Directory to support LAPS. For links to parts 1 and 3, see the bottom of this post.

# Configuring Active Directory for LAPS

## Extend the AD Schema

In the last post, we configured the client installation. While there is no server-side software to run as far as ensuring the system operates, there are a number of changes required to Active Directory in order to allow for secure password storage against a computer object. As part of this, we'll be adding two attributes to Active Directory's `computer` class:

*   ms-MCS-AdmPwd - this attribute stores the password itself
*   ms-MCS-AdmPwdExpirationTime - this attribute stores the time after which the computer's password should be changed

In part 1, you would've installed the LAPS management tools onto a computer. Connect to this computer as a member of the *Schema Admins* Active Directory group. Open an elevated Windows PowerShell prompt and import the AdmPwd.PS module: 

![AD-1]({{ site.baseurl }}/assets/AD-1.png) 

Once imported, you must extend the AD schema using the `Update-AdmPwdADSchema` cmdlet: 

![AD-2]({{ site.baseurl }}/assets/AD-2.png)

## Configure Active Directory Permissions

By default, the vast majority of Active Directory computer object properties can be read by any user with Domain Users privileges or above. If we're storing local administrator passwords, this isn't necessarily a desired situation. Let's dig into AD and restrict who can actually view this newly created ms-Mcs-AdmPwd attribute:

1.  Open ADSIEdit.msc
2.  Right click the ADSI Edit node and choose *Connect to...*. <br />![AD-3]({{ site.baseurl }}/assets/AD-3.png)
3.  Ensure *Default naming context* is selected and click **OK** <br />![AD-4]({{ site.baseurl }}/assets/AD-4.png)
4.  Drill down the tree to find the organizational unit that contains your computer objects <br />![AD-5]({{ site.baseurl }}/assets/AD-5.png)
5.  Right click the OU and select Properties <br />![AD-6]({{ site.baseurl }}/assets/AD-6.png)
6.  On the *Security* tab, choose **Advanced** <br />![AD-7]({{ site.baseurl }}/assets/AD-7.png)
7.  For each non-administrative group that currently has it checked (if any), remove the *All extended rights* permission: <br />![AD-8]({{ site.baseurl }}/assets/AD-8.png)
8.  Next, grant computers the ability to update their password attribute using `Set-AdmPwdComputerSelfPermission` <br />![AD-9]({{ site.baseurl }}/assets/AD-9.png)
9.  Now it's time to grant rights to users to allow them to retrieve a computer's password. I'm going to use two different groups for this, one for servers and one for clients: <br />![AD-10]({{ site.baseurl }}/assets/AD-10.png)

# What Else?

* [See part 1 for installing LAPS](/deploying-the-local-administrator-password-solution-part-1/) 
* [See part 3 for configuring Group Policy and operating LAPS](/deploying-the-local-administrator-password-solution-part-3/)