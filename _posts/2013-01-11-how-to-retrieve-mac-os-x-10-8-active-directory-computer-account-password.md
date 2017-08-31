---
layout: post
title: How to Retrieve Mac OS X 10.8 Active Directory Computer Account Password
date: 2013-01-11 12:31:21.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- Apple
- Automation
- Mac OS X
- Scripting
- Security
meta:
  dsq_thread_id: '1019182587'
  _edit_last: '1'
  og:image: ''
  og:description: ''
  og:title: ''
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2013/01/how-to-retrieve-mac-os-x-10-8-active-directory-computer-account-password/
---
Active Directory bound Mac OS X computers traditionally stored the computer account credentials in /Library/Preferences/DirectoryService/ActiveDirectory.plist. As of OS X 10.7, this has been moved to the (theoretically) more secure System Keychain. Because of this, a slightly different method has to be employed to extract the computer account password. Fortunately, this is easier than the old plist method. Simply run the following command in Terminal as root (or any sudoer):

    security find-generic-password –sw "/Active Directory/MARGIESTRAVEL" /Library/Keychains/System.keychain

This command broken down:
* `security` is the application for accessing keychain information. It is automatically decrypted and presented in a somewhat readable format as though you were using Keychain Access.app

* `find-generic-password` retrieves the contents of a Keychain item

* `-w` is an option that tells security to only return the password, and to StdOut. By default, security returns generic info to StdOut and returns the password to StdErr. –w "fixes" this
* `-s` is an option that tells security to return the item of service type `<string>`

* `"/Active Directory/MARGIESTRAVEL"` is the name of the keychain item in string format. Obviously this is the Directory Service path to the domain (you can verify by running dscl)

* `"/Library/Keychains/System.keychain"` is the filesystem path to the System keychain itself

If you want your script to be redistributable (hint: you do), you can do the following to get the name of the computers’ domain:

    dscl localhost –list "/Active Directory"

This will return the name of the computer’s domain. You could update the command above to implement this like so:

    security find-generic-password –w -s "/Active Directory/$(dscl localhost –list '"/Active Directory")" /Library/Keychains/System.keychain

All we’re doing here is replacing the "MARGIESTRAVEL" component of the name of the keychain item with the command that asks dscl for the name of the computer’s domain. Easy!

P.S. here’s how to get the AD computer account name as well. Doesn’t deserve a blog post, but an interesting little snippet:

    security find-generic-password -s "/Active Directory/MARGIESTRAVEL" /Library/Keychains/System.keychain | grep -m 1 "acct" | sed -e 's/"acct"<blob>\="//' -e 's/"//' | tr -d ' '


(This one just returns the whole keychain item and looks for the first line containing "Acct". It then removes all the useless info around the outside of the computer name.
<aside class="aside-info">Update: Thanks to commenter Joshua for this easier/safer/cleaner methord for finding the computer name:
    dsconfigad -show | sed -n 's/Computer.Account\ \{17\}=\ //p'
</aside>
