---
layout: post
title: Deploying the Local Administrator Password Solution Part 1
date: 2015-05-06 12:38:08.000000000 +10:00
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
- Security
- Server 2012 R2
meta:
  og:image: ''
  og:description: ''
  _edit_last: '1'
  og:title: ''
  dsq_thread_id: '3741140461'
  amazonS3_cache: a:7:{s:64:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-11.png";i:942;s:64:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-21.png";i:943;s:63:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-3.png";i:944;s:63:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-4.png";i:945;s:63:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-5.png";i:946;s:63:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-Install-6.png";i:947;s:55:"//flamingkeys.com/wp-content/uploads/2015/05/LAPS-7.png";i:949;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
In this series of three posts, I demonstrate the installation and configuration of Microsoft's Local Administrator Password Solution (LAPS). This is the first post that covers installation of the software on management computers and clients. For links to parts 2 and 3, see the bottom of this post.

# What is LAPS?

LAPS is a solution to change the local administrator (SID -500) password on all domain joined computers to something complicated, unique, and regularly changed. Previously available only to those lucky customers who have Microsoft Premier agreements, Local Administrator Password Solution (LAPS) has recently been published for all customers as part of [MSA 3062591](https://technet.microsoft.com/en-us/library/security/3062591.aspx).

*   [Download LAPS](https://www.microsoft.com/en-us/download/details.aspx?id=46899)
*   [Read more at KB3062591](https://support.microsoft.com/en-us/kb/3062591)

The purpose of LAPS is, first and foremost, to secure Active Directory environments by ensuring that all computers have different and complex local administrator passwords. LAPS works by creating an attribute against the `computer` class in Active Directory. Permissions must be explicitly granted to allow users to view this password attribute. The benefit of this solution is that it's very simple, relying purely on Active Directory and its supporting infrastructure.

# What do I need?

You'll need at least one management computer, and at least one client computer (which can be the same machine). In my `margiestravel.com` lab, I have the following setup:

*   MTDC01 (Windows Server 2012 R2 Domain Controller) - LAPS Management Computer
*   MTCL01 (Windows 7 Client) - LAPS Client

We'll install the LAPS fat client, PowerShell module and Group Policy templates onto the Server 2012 R2 machine, and will work with MTCL01 as our client endpoint that will have their passwords changed. It's important to note that this procedure is identical regardless of whether your 'client' computers are running a desktop or server operating system. You'll also want to grab the bits I link to above from Technet, including the following components at a bare minimum:

*   `LAPS.x64.msi` for Windows 64-bit management computers
*   `LAPS.x86.msi` for Windows 32-bit management computers

# How is it installed?

Installing the LAPS infrastructure couldn't be much more trivial. Grab the installers and next/next/finish your way through them:

## Management Computers

![LAPS Install 1]({{ site.baseurl }}/assets/LAPS-Install-11.png) 

Read and accept the EULA over a strong glass of red: 

![LAPS Install 2]({{ site.baseurl }}/assets/LAPS-Install-21.png) 

Choose the components you'd like installed. In my lab, I'm going to deploy the whole lot (because why not?). 

![LAPS Install 3]({{ site.baseurl }}/assets/LAPS-Install-3.png) 

Once you're happy, click **Install**, accept the UAC prompt (you *do* have UAC enabled, [right](http://www.wilderssecurity.com/threads/why-do-idiots-disable-uac-claim-its-not-a-security-function.317697/)?) and you'll be on your way.

## Client Computers

These are the machines that will have their passwords changed. It's quite a simple install here, we just need to grab a DLL and register it. Because I'm ~~lazy~~ efficient, let's create a group policy to run this installer. In a real environment, you'd definitely use ConfigMgr or something similar for this: 

![LAPS Install 4]({{ site.baseurl }}/assets/LAPS-Install-4.png)

Be sure to edit the x86 package to **uncheck** the *Make this 32-bit X86 application available to Win64 machines.* option. This will ensure that 64-bit computers get the 64-bit DLL, and 32-bit machines get the 32-bit DLL. Super stuff, that. 

![LAPS Install 5]({{ site.baseurl }}/assets/LAPS-Install-5.png) 

![LAPS Install 6]({{ site.baseurl }}/assets/LAPS-Install-6.png) 

Once installed, you'll see the *Local Administrator Password Solution* appear in Programs and Features: 

![LAPS 7]({{ site.baseurl }}/assets/LAPS-7.png)

## What Else?

* [See part 2 for configuring Active Directory](https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-2/) 
* [See part 3 for configuring Group Policy and operating LAPS](https://flamingkeys.com/2015/05/deploying-the-local-administrator-password-solution-part-3/)