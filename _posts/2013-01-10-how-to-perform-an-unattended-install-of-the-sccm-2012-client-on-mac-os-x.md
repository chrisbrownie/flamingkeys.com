---
layout: post
title: How to perform an unattended install of the SCCM 2012 Client on Mac OS X
date: 2013-01-10 11:50:53.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Apple
- Automation
- Bash
- ConfigMgr
- Mac OS X
- SCCM
- SCCM 2012 SP1
- Scripting
- System Center
- windows
meta:
  dsq_thread_id: '1017129276'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2013/01/how-to-perform-an-unattended-install-of-the-sccm-2012-client-on-mac-os-x/
---
<div class="alert alert-warning" role="alert">
I'm aware this method is not working for the latest version of the client. When I have a Mac available to me I will update this post.
</div>

Installing the Configuration Manager client on Mac OS X is a new feature of SCCM 2012 SP1. Unfortunately the installation process is nowhere near as easy as installing for a PC. The steps go something like this:

1. Download a DMG of the client installer
2. Run the install script
3. Run the script to get a certificate from the CA

Fortunately, these steps are quite easy to script.

Before you proceed any further, be sure that manual deployments of the Mac client work. [James Bannan](http://twitter.com/jamesbannan) has an excellent guide on this [over on his website](http://www.jamesbannanit.com/2012/10/enrol-mac-os-x-clients-in-configuration-manager-2012-sp1/).

In this instance, we’ll assume the script is being run as part of an automated deployment, so it would be useful to have the dmg file hosted somewhere, as well as credentials that can request the certificate. For this, I’ve created a directory in my SCCM 2012 SP1 MP’s Default Web Site called "MacClient", with contents as follows:

{% highlight powershell %}
Get-ChildItem C:\inetpub\wwwroot\MacClient
{% endhighlight %}

    Mode LastWriteTime Length Name
    ---- ------------- ------ ----
    -a--- 10/01/2013 10:53 AM 50 credentials.txt
    -a--- 6/12/2012 5:39 PM 4776423 macclient-5.0.0.7804.1000.dmg
    -a--- 10/01/2013 9:29 AM 29 MacClient-dmg-latest.txt


This is accessible at [https://sccm.margiestravel.com/MacClient/](https://sccm.margiestravel.com/MacClient/). I had to add a MIME type for `.DMG` so that IIS would allow it to be downloaded. This is a simple addition to the `<system.webServer>` section of the `web.config` file in this directory:
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <staticContent>
      <mimeMap fileExtension=".dmg" mimeType="file/download" />
    </staticContent>
    <directoryBrowse enabled="true" />
  </system.webServer>
</configuration>
{% endhighlight %}

`Credentials.txt` is a two-line text file, the first line of which contains a username in the format `DOMAIN\Username` and the second a password. For example:

    MARGIESTRAVEL\SccmCertDeployment
    Pa$$w0rd

The `MacClient-dmg-latest.txt` file is called by the client script. This text file contains the just the filename of the latest version of the SCCM client DMG, located in the same directory. This means that you can keep older installers in place for whatever reason, while the automatic script will be sure to grab the latest version. Alongside this is `macclient-5.0.0.7804.1000.dmg`, the DMG file containing the Mac Client for SCCM 2012 SP1.

As you can see, the majority of the operation is performed server-side, which means this script can be built into your OS X deployment/post-image procedures and *should* never need to be updated. All you have to do is drop a new DMG in, update the text file, and occasionally you’ll likely want to change the credentials that request the certificate. (I know typically the computer’s user would request the certificate themselves with their own AD credentials, but in this case I’m using the same set of credentials for everyone. The script could certainly be modified to prompt for a set of AD credentials from the user)
[Here’s a copy of the script](http://www.flamingkeys.com/wp-content/uploads/2013/01/InstallMacCMClient.txt). Lines 18-22 are the only ones that should require changing. Here’s a brief description of what each variable does:
* `appDistributionSite` is the base URL of the folder containing the credentials.txt and MacClient-dmg-latest.txt files
* `configMgrServer` is the FQDN of your SCCM 2012 SP1 Management Point that’s configured for internet access
* `scriptVersion` is just the version number of this script. You may wish to increment this if you ever make changes to the script, as scriptVersion is reported to the SCCM server as the client’s User Agent. You can view this in your IIS logs
* `userAgent` is a string presented to the web server when downloading files
* `workingDirectory` defines where on the client files will be placed while installing the SCCM client. The script will create this folder if it does not exist. The default is `~/CMInstallTemp`

<div class="alert alert-info" role="alert">You can [download a zip file](http://www.flamingkeys.com/wp-content/uploads/2013/01/MacClient.zip) of the script and text files ready to go. You’ll just have to grab the DMG file from your SCCM 2012 SP1 install media and place it on your web server with them.</div>
