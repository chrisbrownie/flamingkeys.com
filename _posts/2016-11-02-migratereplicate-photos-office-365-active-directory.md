---
layout: post
title: Migrate/Replicate Photos from Office 365 to Active Directory
date: 2016-11-02 11:26:12.000000000 +11:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  _yoast_wpseo_primary_category: ''
  _yoast_wpseo_content_score: '30'
  dsq_thread_id: '5272467087'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

Users can easily set their photos in Office 365 via any number of interfaces. Performing this change in AD is a little more challenging. Fortunately, we can quite easily write these photos back into Active Directory with a little PowerShell script. You'll need the ActiveDirectory module and Exchange Online credentials with any multi-factor *disabled* as Exchange Web Services is used, which does not presently support MFA.

{% gist 2636a296da4599e7afae6de2862ed9c7 %}

