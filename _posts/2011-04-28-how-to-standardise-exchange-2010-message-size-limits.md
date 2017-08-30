---
layout: post
title: How to Standardise Exchange 2010 Message Size Limits
date: 2011-04-28 15:59:50.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Email
- Exchange
- Microsoft
- PowerShell
- Reporting
- Scripting
meta:
  _wp_jd_bitly: http://fla.im/lmsOds
  _wp_jd_target: http://www.flamingkeys.com/2011/04/how-to-standardise-exchange-2010-message-size-limits/?utm_campaign=twitter&utm_medium=twitter&utm_source=twitter
  robotsmeta: index,follow
  _jd_wp_twitter: 'Flaming Keys |  Blog Post Edited: How to Standardise Exchange 2010
    Message Size Limits http://fla.im/lmsOds'
  _jd_post_meta_fixed: 'true'
  dsq_thread_id: '290265580'
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
There has long been "somewhere" in my exchange organisation that has a 10MB message size limit, rather than our *supposed* standard 15MB. Finally this annoyed me enough to do something about it. Because I’m all for saving time, I figured I’d script it. I trawled through Exchange and found all the places where it is possible to limit the size of messages (if I’ve missed any, please let me know!). From here I wrote a script to find the size limit on every instance of each of these possible places. As far as I can see, there are three places of concern in an Exchange 2010 organization: Send Connectors, Receive Connectors and the Global Transport Configuration.

## Evaluating the Current Limits

It is possible to use three simple cmdlets to retrieve any and all data that is required to achieve my goal...But that wouldn’t be much fun, would it? No sense running these three and trawling through the results each time. See [Making it Pretty](#making-it-pretty) below for the final result.

*Send Connectors* are present at the organization level (that is, they are not specific to a particular server, and can be assigned to one or more). To retrieve send connectors, simply use the `Get-SendConnector` cmdlet. We are looking at the `MaxMessageSize` property:

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/04/image.png)

*Receive Connectors* are server-specific. Fortunately, however, this is irrelevant in our case: `Get-SendConnector` and the `MaxMessageSize` property are this time of interest:

![]({{ site.cdnbaseurl }}wp-content/uploads/2011/04/image1.png)

Finally let’s investigate the Global Transport Config. This applies to *All* transports in the Organization. You can retrieve its settings by running the cmdlet `Get-TransportConfig`. We’re interested in the properties `MaxReceiveSize` and `MaxSendSize`:
![]({{ site.cdnbaseurl }}wp-content/uploads/2011/04/image2.png)

## Making it Pretty

It’s pretty easy to trawl through the above results and investigate them...but where’s the fun in that? Or perhaps you want to do this as part of your regular Exchange Organisation health check report (you do have one, don’t you?)? I’m going to start by finding the smallest message size limit, since this is the one that we’re concerned about here. Small size limits act as a bottle-neck in the truest sense of the word.

I’ve put all my relative limits into an object array that looks like this:

Type                 Server           Name                                           Limit
----                 ------           ----                                           -----
SendConnector        Organization     EdgeSync - Default-First-Site-Name to Internet 10 MB (10,485,760 bytes)
SendConnector        Organization     EdgeSync - Inbound to Default-First-Site-Name  unlimited
ReceiveConnector     EX01             Default EX01                                   15 MB (15,728,640 bytes)
ReceiveConnector     EX01             Client EX01                                    15 MB (15,728,640 bytes)
ReceiveConnector     EX02             Default EX02                                   15 MB (15,728,640 bytes)
ReceiveConnector     EX02             Client EX02                                    15 MB (15,728,640 bytes)
ReceiveConnector     EX01             Anonymous Authentication Networks              15 MB (15,728,640 bytes)
ReceiveConnector     EX02             Anonymous Authentication Networks              15 MB (15,728,640 bytes)
TransportConfig      Organization     Maximum Receive Size                           15 MB (15,728,640 bytes)
TransportConfig      Organization     Maximum Send Size                              15 MB (15,728,640 bytes)

Let’s grab the smallest limit from here (Note: Usually you would use `Measure-Object` here, but "unlimited" is, unfortunately, not a numerical value):

{% highlight powershell %}
$smallest = $limits | Sort-Object Limit | Select-Object –first 1
$smallest
{% endhighlight %}

Now we know which is the very smallest size of message that can fully traverse the organization as well as one of the places it exists. To find all the places this smallest limit exists, we run this line:

{% highlight powershell %}
$limits | Where {$_.Limit -eq ($smallest).Limit }
{% endhighlight %}

Download the finished script [here]({{ site.cdnbaseurl }}/wp-content/uploads/2011/04/Get-MessageSizeLimits.ps1_.txt)

And of course, the output:

    The maximum size of any email that can fully traverse your Exchange Organization is 10 MB (10,485,760 bytes)
    This is because of the following object(s):

    Type             Server       Name                                           Limit
    ----             ------       ----                                           -----
    SendConnector    Organization EdgeSync - Default-First-Site-Name to Internet 10 MB (10,485,760 bytes)

    You should raise the maximum size of message allowed across the object(s) above if you are having mail
    delivery issues of large items. Note that this script will not check non-exchange transports such as
    AntiVirus and Smart-hosts.
    Raise the size of message allowed across Send Connectors with Set-SendConnector -MaxMessageSize
    Raise the size of message allowed across Receive Connectors with Set-ReceiveConnector -MaxMessageSize
    Raise the size of message allowed across the global transport config with Set-TransportConfig -MaxReceiveSize
		and Set-TransportConfig -MaxSendSize</pre>
