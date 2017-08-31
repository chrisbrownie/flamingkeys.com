---
layout: post
title: Office 365 live in Australia
date: 2015-03-31 04:20:16.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Microsoft
- Office 365
meta:
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  dsq_thread_id: '3641585967'
  amazonS3_cache: a:1:{s:54:"//flamingkeys.com/wp-content/uploads/2015/03/gcmap.gif";i:922;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2015/03/office-365-live-in-australia/
---
After what seems like forever, Microsoft have finally lit up Office 365 in a couple of Aussie datacentres. First [announced in December](http://news.microsoft.com/en-au/2014/12/08/locallyhostedo365andcrm/), Microsoft committed to a March 2015 release. Well, it's March still&hellip;just. 
While reasons abound to want to have your tenant located on our glorious, metadata-retaining shores, the proof is, as they say, in the pudding. I spun up a demo tenant this morning after the announcement, and after testing my new tenant's SharePoint site, the performance gains (as far as latency goes) are quite impressive. From my (awful) Wi-Fi connection at the [Office 365 Summit](http://summit.office.com/) in Sydney, I fired off a traceroute to make sure my new tenant was, in fact, in Australia:

**Tenant Provisioned March 31, 2015**

    C:\>tracert xxx.sharepoint.com

    Tracing route to prodnet324-328ipv4a0002.sharepointonline.com.akadns.net [104.146.164.27]
    over a maximum of 30 hops:

      1    45 ms   194 ms    74 ms  10.177.128.1
      2    14 ms    17 ms     7 ms  sonartech2.tmx.com.au [203.9.152.1]
      3    91 ms   133 ms   266 ms  203.9.248.229
      4   513 ms   243 ms   157 ms  4826.**syd**.equinix.com [202.167.228.74]
      5   286 ms   189 ms   147 ms  ten-0-2-0-0.cor03.**syd**03.nsw.VOCUS.net.au [114.31.192.42]
      6   495 ms   137 ms    66 ms  bundle-100.bdr04.**syd**03.nsw.VOCUS.net.au [114.31.192.45]
      7    11 ms    27 ms     8 ms  as12076.cust.bdr02.**syd**03.nsw.VOCUS.net.au [114.31.200.14]
      8   208 ms   104 ms   304 ms  ae7-0.**syd**-96cbe-1b.ntwk.msn.net [191.234.84.171]
      9   180 ms   125 ms   276 ms  xe-1-2-2-0.**mel**01-96cbe-1b.ntwk.msn.net [191.234.81.59]
    10   232 ms    33 ms    26 ms  ae0-0.**mel**01-96cbe-1a.ntwk.msn.net [191.234.80.56]
    11     *        *        *     Request timed out.

This trace wraps up in what can be reasonably inferred to be Melbourne. I'd be pretty comfortable suggesting that this tenant lives in Microsoft's Melbourne datacentre.

**Tenant Provisioned Late 2014**

    C:\>tracert xxx.sharepoint.com

    Tracing route to prodnet26-28ipv4a0000.sharepointonline.com.akadns.net [191.234.222.41]
    over a maximum of 30 hops:

      1   265 ms    43 ms    10 ms  10.177.128.1
      2    10 ms     8 ms    21 ms  sonartech2.tmx.com.au [203.9.152.1]
      3   741 ms   221 ms   276 ms  203.9.248.229
      4   141 ms   136 ms   188 ms  4826.**syd**.equinix.com [202.167.228.74]
      5    60 ms    90 ms    39 ms  ten-0-2-1-6.cor03.**syd**03.nsw.VOCUS.net.au [114.31.192.52]
      6    29 ms    34 ms    12 ms  bundle-100.bdr04.**syd**03.nsw.VOCUS.net.au [114.31.192.45]
      7    35 ms    23 ms    24 ms  as12076.cust.bdr02.**syd**03.nsw.VOCUS.net.au [114.31.200.14]
      8   116 ms    78 ms   214 ms  ae7-0.**syd**-96cbe-1b.ntwk.msn.net [191.234.84.171]
      9   140 ms    49 ms    23 ms  191.234.85.71
    10   203 ms   168 ms    55 ms  ae0-0.**mel**01-96cbe-1a.ntwk.msn.net [191.234.80.56]
    11   365 ms    79 ms    69 ms  xe-7-0-0-0.**per**01-96cbe-1a.ntwk.msn.net [191.234.81.30]
    12   230 ms   209 ms   122 ms  xe-2-1-3-100.**sge**-96cbe-1a.ntwk.msn.net [191.234.82.189]
    13   265 ms   282 ms   268 ms  ae0-0.**sge**-96cbe-1b.ntwk.msn.net [207.46.38.76]
    14   191 ms   477 ms   193 ms  ae1-0.**sg**2-96cbe-1b.ntwk.msn.net [191.234.80.147]
    15     *        *        *     Request timed out.

As compared to the trace above, this trace bounces through what I infer to be Perth and then Singapore. I don't know how your geography is, but Melbourne is significantly closer to my Sydney location than Singapore (even via Perth) is:

![]({{ site.baseurl }}/assets/gcmap.gif)

In addition to this, there is a significant latency benefit to these local servers. I haven't had a chance to performance test mail or SharePoint or anything like that yet, but even those last-hop pings are quite impressive - around 200ms in Singapore, and around 30 for the Melbourne hosts. This will likely make a lot of online-mode Outlook users very happy. Massive congratulations to Microsoft on (finally) getting this launched, I know it'll make a lot of prospective Australian customers quite happy. As for tenant moves - I'm advised that this will be happening to tenants that have "Australia" set as their location, with a six week warning being sent to the tenant administrator. As to whether there will be an opt-out for this or not, I'm not sure.
