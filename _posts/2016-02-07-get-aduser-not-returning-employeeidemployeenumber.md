---
layout: post
title: Get-ADUser not returning EmployeeID/EmployeeNumber
date: 2016-02-07 06:43:26.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- AD-DS
- Annoyances
- Computers
- PowerShell
- Scripting
meta:
  og:description: ''
  og:title: ''
  _edit_last: '1'
  og:image: ''
  dsq_thread_id: '4558582316'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/02/get-aduser-not-returning-employeeidemployeenumber/
---
This one took longer than I care to admit to work out... Get-ADUser was returning blank values for EmployeeID/EmployeeNumber for all users. Very strange. Run PowerShell as Administrator to fix this.
