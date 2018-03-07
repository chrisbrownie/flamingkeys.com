---
layout: post
title: Copy Files to Office 365 Groups with PowerShell
---

[Office 365 Groups]({% post_url 2018-01-23-office-365-groups-best-practices-for-admins %}) are a great upgrade on the traditional SharePoint Site/Shared Mailbox/Shared OneDrive folder/whatever you were previously using to share stuff. They're a perfect candidate for migrating a department's "network drive" (as the users would call it) to. Perhaps your HR department have `S:\Human Resources`, and Finance have `S:\Finance`. Or worse, perhaps HR have `J:\` (or whatever the Service Desk person mapped it to) and Finance have `X:\` (or some other drive). Groups are a great way of centralising file storage and making files accessible to approved users from wherever they happen to be.

In migrating large (several hundred GB) department file systems to Groups, after a bit of Googling and a bit of adding my own bits, I came up with the below script to allow an administrator (or even user, realistically) to copy a large volume of files to an Office 365 Group using PowerShell. 

## Getting Started

First, you'll need to install the [SharePoint Patterns & Practices](https://github.com/SharePoint/PnP-PowerShell) `SharePointPnP` module. This can be installed using PowerShellGet on Windows 10:

{% gist e608c649583b52b856c78943b7ec862e %}

Secondly, download the script to a folder on your computer by either selecting the text and saving it, or running the PowerShell code beneath it:

{% gist 1ee513f4b0f9ebc76f0fe84d032c3cb9 %}

{% gist 31fe9b11e598f597ca3828eaf93b4a46 %}

Thirdly, run the script with the appropriate parameters:

{% gist 6156e57ae2bebbfca3fa835d525bf1e4 %}

Done! Now it'll run and copy your files up. Errors will need to be reviewed and remediated manually, though the PnP module does a pretty decent job of telling you what went wrong.

## Caveats

* This script (or, rather, the SharePoint Patterns & Practices module) doesn't support moving files over 256 MB. You'll need to move these manually based on the errors generated by this script