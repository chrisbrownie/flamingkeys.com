---
layout: post
title: Remove Default PowerShell Aliases
date: 2015-08-11 08:00:32.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- PowerShell
- windows
meta:
  og:description: ''
  og:image: ''
  dsq_thread_id: '4021188167'
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  og:title: ''
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/08/remove-default-powershell-aliases/
---
PowerShell has a cheeky approach to cross-OS compatibility. Two of my favourite utilities; [wget](https://en.wikipedia.org/wiki/Wget), and [curl](https://en.wikipedia.org/wiki/CURL), are aliased to PowerShell's [Invoke-WebRequest](https://technet.microsoft.com/en-us/library/hh849901.aspx) cmdlet. This is nice and all, but `Invoke-WebRequest` doesn't work anything at all like curl or wget do, and sometimes I just want to be able to download a file without mucking around with parameters. Even after installing wget.exe and curl.exe into my path, PowerShell grabs ahold of the unqualified names (that is, no extension):

{% highlight powershell %}
PS C:\> wget https://abc.xyz
{% endhighlight %}

    StatusCode        : 200
    StatusDescription : OK
    Content           : <!doctype html>
                        <!--[if lt IE 7]> <html class="ie7 oldie" lang="en"> <![endif]-->
                        <!--[if IE 8]>    <html class="ie8 oldie" lang="en"> <![endif]-->
                        <!--[if IE 9]>    <html class="ie9 oldie" lang="en">...
    RawContent        : HTTP/1.1 200 OK
                        Vary: Accept-Encoding
                        X-Content-Type-Options: nosniff
                        X-XSS-Protection: 1; mode=block
                        Age: 2313
                        Alternate-Protocol: 443:quic,p=1
                        Transfer-Encoding: chunked
                        Accept-Ranges: none

It's doing this using aliases:

{% highlight powershell %}
PS C:\> Get-Alias wget,curl | ft -auto
{% endhighlight %}

    CommandType Name                      Version Source
    ----------- ----                      ------- ------
    Alias       wget -> Invoke-WebRequest
    Alias       curl -> Invoke-WebRequest

Enter PowerShell profiles. These are a great way to perform an action at the start of every PowerShell session. Because the aliases for `wget` and `curl` are built into PowerShell, we can't simply disable them system-wide. What needs to occur is a per-session removal of them. Let's add two commands to my profile that will do this:

{% highlight powershell %}
"Remove-Item alias:wget; Remove-Item alias:curl" | Out-File $profile -Append -NoClobber
{% endhighlight %}

If you receive an error along the lines of "Could not find a part of the path" it probably means your profile doesn't exist. Create it with the following:

{% highlight powershell %}
PS C:\> New-Item $profile -ItemType file -Force
{% endhighlight %}

After the changes above, we now see this:
{% highlight powershell %}
PS C:\> wget https://abc.xyz --no-check-certificate
{% endhighlight %}

    --2015-08-11 09:32:46--  https://abc.xyz/
    Resolving abc.xyz (abc.xyz)... 2404:6800:4006:800::200e, 216.58.220.142
    Connecting to abc.xyz (abc.xyz)|2404:6800:4006:800::200e|:443... connected.
    WARNING: cannot verify abc.xyz's certificate, issued by 'CN=Google Internet Authority G2,O=Google Inc,C=US':
      Unable to locally verify the issuer's authority.
    HTTP request sent, awaiting response... 200 OK
    Length: unspecified [text/html]
    Saving to: 'index.html'
    index.html                        [  <=>                                               ]  10.54K  44.7KB/s   in 0.2s
    2015-08-11 09:32:48 (44.7 KB/s) - 'index.html' saved [10793]

Much better!
