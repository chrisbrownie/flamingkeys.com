---
layout: post
title: Monitoring XenServer with SCOM 2012
date: 2012-06-06 11:30:00.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Microsoft
- Monitoring
- OpsMgr
- SCOM
- Security
- System Center
- Virtualization
meta:
  Hide SexyBookmarks: '0'
  robotsmeta: index,follow
  _edit_last: '1'
  Hide OgTags: '0'
  dsq_thread_id: '715304514'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
If you’ve tried to to monitor your XenServer hosts from SCOM 2012, you’ll note that the guides are geared towards SCOM 2007. The steps below will help you to get SCOM 2012 monitoring XenServer (version 6.0.2, in this instance). The first couple of steps are the same as for SCOM 2007. 

## Install the System Center Integration Pack On each of your XenServer hosts

Perform the following steps to install the System Center Integration Pack

1.  [Download the iso](http://www.citrix.com/lang/English/lp/lp_2305185.asp) into your home folder on the XenServer host (using wget, SCP or similar)
2.  Create a temporary mount point and mount the iso file:

    mkdir /tmp/isomount -o loop ~/XenServer-6.0.0.2-integration-suite.iso /tmp/iso

3.  Install the integration tools:

    /tmp/iso/install.sh

4.  Remove the iso and mount:

    cd ~umount /tmp/isorm -rf /tmp/isorm XenServer-6.0.0.2-integration-suite.iso

## Trust the XenServer SSL Certificates 

On your SCOM management server, browse to this URL for each of your XenServers (each server has a unique certificate by default, and you need to install each): [http://YourXenServer/scom/install_certificate.html](http://YourXenServer/scom/install_certificate.html). Follow the steps on this page for each server. This will ensure that communications can be secured and trusted over SSL. 

## Install the Pre-Requisite Management Packs 

This is where we deviate from the SCOM 2007 install steps. The `Microsoft.Linux.Library` management pack is not present in SCOM 2012 by default, nor can you download it from the in-program catalog.

1.  From TechNet, grab the latest version of the [System Center Monitoring Pack for System Center Operations Manager 2007 R2 Cross Platform](http://www.microsoft.com/en-us/download/details.aspx?id=18891) (quite the mouthful!). Download the `SCOM 2007 R2 Cross Platform MPs.msi` file from this list
2.  Run the MSI on your SCOM management server, and a bunch of cross platform management packs will be extracted to `C:\Program Files (x86)\System Center Management Packs\SCOM 2007 R2 Cross Platform MPs\`.
3.  Next, open the Operations Console and select the *Administration* workspace. Right click on any tree node and click "Import Management Packs…"
4.  Click on Add and choose "Add from Disk". When asked if you want to search for dependencies, choose "No"
5.  Browse to the path above (or wherever you extracted the management packs to) and choose "Microsoft.Linux.Library.mp"
6.  Click Install

## Install the XenServer Management Packs 

On your SCOM management server, browse to one of your XenServer hosts: [http://xenserver/scom/install_managementpacks.html](http://xenserver/scom/install_managementpacks.html). Follow the steps on this page to install the management packs. 

## Add XenServer Hosts to SCOM 

Follow the instructions on the [http://xenserver/scom/adding_machine.html](http://xenserver/scom/adding_machine.html) page to add your hosts to SCOM.
