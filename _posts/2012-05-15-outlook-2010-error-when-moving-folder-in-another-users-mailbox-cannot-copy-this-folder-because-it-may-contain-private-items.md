---
layout: post
title: 'Outlook 2010 Error when moving folder in another user’s mailbox: "Cannot copy
  this folder because it may contain private items"'
date: 2012-05-15 10:30:00.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Email
- Exchange
- Outlook
- windows
meta:
  dsq_thread_id: '689604851'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2012/05/outlook-2010-error-when-moving-folder-in-another-users-mailbox-cannot-copy-this-folder-because-it-may-contain-private-items/
---
This error occurs when you try to move a folder in another user’s mailbox that contains private items (duh!). Upon Googling (Binging) this you’ll see people sprouting about how this is a bug that’s been around forever, it’s something Microsoft should fix, yada yada yada. The fact of the matter is, private items are private for a reason, and by design. You can mark items private so that without your express consent, no other user – not even one with "Full Control" over your mailbox – can see them. Allowing users to see and move private items is a somewhat trivial exercise, but the solution is relatively evasive on the internet, so here goes:
Open Outlook as the user whose mailbox the private items are in. Click on File, Account Settings and then Delegate Access.

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/05/image.png)

Click on Add, then find the account of the user who’s receiving the error. Complete the dialogue so the settings below are set (at minimum):

![]({{ site.cdnbaseurl }}wp-content/uploads/2012/05/image1.png)

Click OK, then OK again. It’s as easy as that! Restart Outlook and you will be able to move the folders and items.

When doing this, I received the following error:
![]({{ site.cdnbaseurl }}wp-content/uploads/2012/05/image2.png)


This is an error that halts the saving of settings, so you need to tell Outlook to ignore this. A simple registry key will suffice:
`HKCU\Software\Microsoft\Office\14.0\Outlook\Preferences`
Create a DWORD `IgnoreSOBError` with value `1`.
Restart Outlook and you’ll be able to perform the actions above without seeing this error.
