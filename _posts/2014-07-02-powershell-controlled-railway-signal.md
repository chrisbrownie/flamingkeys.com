---
layout: post
title: PowerShell Controlled Railway Signal
date: 2014-07-02 13:00:21.000000000 +10:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  _oembed_d0ad9b968438f700a5559124151d05a9: "{{unknown}}"
  _oembed_28d4f5858a32c9a44c18d11965c863a4: "{{unknown}}"
  _oembed_da60f3f23c0c07fc269e8efd51b30cf6: "{{unknown}}"
  _oembed_77e11d2a6a16c8f15165a0e87b995bfa: "{{unknown}}"
  _edit_last: '1'
  dsq_thread_id: '2812056760'
  _oembed_6b1a182c1b18a2edfe92509798f59551: "{{unknown}}"
  _oembed_2e137de3fdb135471266aa658e7193cf: "{{unknown}}"
  _oembed_45860f2ff800fbd1f5dee5cd62dcb95e: "{{unknown}}"
  _oembed_f548adc72c0952b8c8d5831203af2057: "{{unknown}}"
  _oembed_f51c5689f2f2e999b220ffba37129514: "{{unknown}}"
  _oembed_42eb02c7a898de7c9950f42ff2a8e4ca: "{{unknown}}"
  _oembed_372d5e82233209f2774aa6741026a310: "{{unknown}}"
  _oembed_8504ec2d0cf3b42951cb3df98cbb461a: "{{unknown}}"
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2014/07/powershell-controlled-railway-signal/
---
So tonight on Twitter, [SlipperySeal](https://twitter.com/SlipperySeal) decided to unleash his Raspberry Pi controlled railway signals upon us. Using a [web page](http://pimpmylight.catchpole.net/), you can enable or disable the lights. I decided to take it one step further and hijack the page with a cheeky bit of PowerShell. I pulled out the URLs called when you update the lights, and used the Start-Sleep cmdlet to time my fake clicks.

    http://pimpmylight.catchpole.net/?update=green
    http://pimpmylight.catchpole.net/?update=orange
    http://pimpmylight.catchpole.net/?update=red

The PowerShell to do this was quite simple: 

{% highlight powershell %}
$wc = New-Object System.Net.WebClient 
$uri = "http://pimpmylight.catchpole.net/?update=green"
while ($true) { 
  $null = $wc.DownloadString($uri); 
  "Green!"; 
  Start-Sleep -milliseconds 500 
}
{% endhighlight %}

Three copies, running at three different time intervals, and we had this:

<iframe width="560" height="315" src="//www.youtube.com/embed/IZR0h7nRaK0" frameborder="0" allowfullscreen=""></iframe>
