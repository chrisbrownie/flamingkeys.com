---
layout: post
title: Remove Empty Folders with PowerShell
date: 2011-08-10 11:52:36.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- File Services
- FSRM
- PowerShell
- Scripting
- tips
meta:
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  robotsmeta: index,follow
  dsq_thread_id: '381814742'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Here’s a quick one for removing all empty folders within a specified folder structure. Theoeretically (untested) it could work with other situations that address PSDrives (try `Get-PSDrive`).
My organisation uses [FSRM](http://technet.microsoft.com/en-us/library/cc755603(WS.10).aspx) to age data on our "scratch" network shares. Files are automatically removed after 30 days. This introduced a small problem with leaving empty folders behind, causing some confusion. The result is a network share with no old files, but with lots of empty folders. This script will delete all folders with no children. (Note: in our production environment, I have preceded this with a script that deletes all unnecessary/folder state files such as [thumbs.db](http://en.wikipedia.org/wiki/Windows_thumbnail_cache) and [.ds_store](http://en.wikipedia.org/wiki/.DS_Store). These files are hidden on their native OSes and while a directory may appear empty, if it contains these files the script below will not handle it (I realise I could have included an exclusion list of files to ignore when deleting directories, but that’s really beyond the scope of what I wanted this script to do).

{% highlight powershell %}
#############################################################################
# Script: Remove-EmptyChildFolders.ps1
# Author: Chris Brown    http://www.flamingkeys.com/
# Date: 10/08/2011
# Keywords: Folders, Empty, Directories, Containers
# Comments:
#
# +------------+-----+---------------------------------------------------------+
# |       Date | Usr | Description                                             |
# +------------+-----+---------------------------------------------------------+
# | 10/08/2011 | CJB | Initial Script                                          |
# +------------+-----+---------------------------------------------------------+
#
# DISCLAIMER
# ==========
# THIS CODE IS MADE AVAILABLE AS IS, WITHOUT WARRANTY OF ANY KIND. THE ENTIRE
# RISK OF THE USE OR THE RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.
#############################################################################
# What's the top level directory we're going to look under?
$rootPath = "C:\Scripts\Scratch\"
# Get each item under the current directory recursively
foreach($childItem in (Get-ChildItem $rootPath -Recurse))
{
	# if it's a folder AND does not have child items of its own
	if( ($childItem.PSIsContainer) -and (!(Get-ChildItem -Recurse -Path $childItem.FullName)))
	{
		# Delete it
		Remove-Item $childItem.FullName -Confirm:$false
	}
}
{% endhighlight %}