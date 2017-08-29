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
---
In this series of three posts, I demonstrate the installation and configuration of Microsoft's Local Administrator Password Solution (LAPS). This is the second post that covers configuration of Active Directory to support LAPS. For links to parts 1 and 3, see the bottom of this post.
<h1>Configuring Active Directory for LAPS</h1>
<h2>Extend the AD Schema</h2>
In the last post, we configured the client installation. While there is no server-side software to run as far as ensuring the system operates, there are a number of changes required to Active Directory in order to allow for secure password storage against a computer object. As part of this, we'll be adding two attributes to Active Directory's *computer* class:
<ul>
<li>ms-MCS-AdmPwd - this attribute stores the password itself</li>
<li>ms-MCS-AdmPwdExpirationTime - this attribute stores the time after which the computer's password should be changed</li>
</ul>
In part 1, you would've installed the LAPS management tools onto a computer. Connect to this computer as a member of the *Schema Admins* Active Directory group. Open an elevated Windows PowerShell prompt and import the AdmPwd.PS module:
<img class="aligncenter size-full wp-image-952" src="{{ site.baseurl }}/assets/AD-1.png" alt="AD-1" width="584" height="228" />
Once imported, you must extend the AD schema using the **Update-AdmPwdADSchema** cmdlet:
<img class="aligncenter size-full wp-image-953" src="{{ site.baseurl }}/assets/AD-2.png" alt="AD-2" width="712" height="106" />
<h2>Configure Active Directory Permissions</h2>
By default, the vast majority of Active Directory computer object properties can be read by any user with Domain Users privileges or above. If we're storing local administrator passwords, this isn't necessarily a desired situation. Let's dig into AD and restrict who can actually view this newly created ms-Mcs-AdmPwd attribute:
<ol>
<li>Open ADSIEdit.msc</li>
<li>Right click the ADSI Edit node and choose *Connect to...*.
<img class="aligncenter size-full wp-image-954" src="{{ site.baseurl }}/assets/AD-3.png" alt="AD-3" width="206" height="106" /></li>
<li>Ensure *Default naming context* is selected and click **OK**
<img class="aligncenter size-full wp-image-955" src="{{ site.baseurl }}/assets/AD-4.png" alt="AD-4" width="367" height="365" /></li>
<li>Drill down the tree to find the organizational unit that contains your computer objects
<img class="aligncenter size-full wp-image-956" src="{{ site.baseurl }}/assets/AD-5.png" alt="AD-5" width="182" height="210" /></li>
<li>Right click the OU and select Properties
<img class="aligncenter size-full wp-image-957" src="{{ site.baseurl }}/assets/AD-6.png" alt="AD-6" width="346" height="223" /></li>
<li>On the *Security* tab, choose **Advanced**
<img class="aligncenter size-full wp-image-958" src="{{ site.baseurl }}/assets/AD-7.png" alt="AD-7" width="385" height="429" /></li>
<li>For each non-administrative group that currently has it checked (if any), remove the *All extended rights* permission:
<img class="aligncenter size-full wp-image-959" src="{{ site.baseurl }}/assets/AD-8.png" alt="AD-8" width="410" height="457" /></li>
<li>Next, grant computers the ability to update their password attribute using *Set-AdmPwdComputerSelfPermission*
<img class="aligncenter size-full wp-image-960" src="{{ site.baseurl }}/assets/AD-9.png" alt="AD-9" width="793" height="82" /></li>
<li>Now it's time to grant rights to users to allow them to retrieve a computer's password. I'm going to use two different groups for this, one for servers and one for clients:
<img class="aligncenter size-full wp-image-961" src="{{ site.baseurl }}/assets/AD-10.png" alt="AD-10" width="894" height="216" /></li>
</ol>
<h1>What Else?</h1>
<a href="https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-1/">See part 1 for installing LAPS</a>
<a href="https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-3/">See part 3 for configuring Group Policy and operating LAPS</a>