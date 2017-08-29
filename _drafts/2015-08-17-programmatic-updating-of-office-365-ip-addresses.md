---
layout: post
title: Programmatic updating of Office 365 IP Addresses
date: 2015-08-17 00:42:44.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Office 365
- Security
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  dsq_thread_id: '4039140847'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Any network admin who has dealt with Office 365 has been through the pain of parsing <a href="https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2" target="_blank">this document</a> to try and allow access to Office 365 while maintaining network security and control. This is a less than fun experience, and the <a href="http://go.microsoft.com/fwlink/p/?linkid=236301" target="_blank">RSS feed</a> providing updates is less than fun.
Fortunately, this can now, with some creativity, become a much less painful experience. The Office 365 team are now offering an <a href="https://support.content.office.net/en-us/static/O365IPAddresses.xml" target="_blank">XML file</a> with all the addresses arranged by service. A creative network administrator could somewhat trivially write an interface to retrieve this file, check the timestamp, and update firewall rules as necessary.
Grab the XML file here: <a href="https://support.content.office.net/en-us/static/O365IPAddresses.xml" target="_blank">https://support.content.office.net/en-us/static/O365IPAddresses.xml</a>.