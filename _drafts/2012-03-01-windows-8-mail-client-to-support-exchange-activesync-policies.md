---
layout: post
title: Windows 8 Mail client to support Exchange ActiveSync Policies
date: 2012-03-01 10:12:19.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- ActiveSync
- Exchange
- Microsoft
- Mobile
- News
- Windows 8
meta:
  dsq_thread_id: '594142118'
  _edit_last: '1'
  robotsmeta: index,follow
  Hide SexyBookmarks: '0'
  Hide OgTags: '0'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
As we all know by now, the <a href="http://windows.microsoft.com/en-US/windows-8/consumer-preview" target="_blank">Windows 8 Consumer Preview</a> was released today (or ysterday for some..time zone dependent). As an Exchange admin, I was excited to dive right into the native email client, and I wasn’t disappointed!
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/03/image.png"><img style="background-image: none; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border: 0px;" title="image" src="{{ site.baseurl }}/assets/image_thumb.png" alt="image" width="244" height="125" border="0" /></a>
The first thing I did was hook up my personal <a href="http://www.microsoft.com/en-au/office365/online-software.aspx" target="_blank">Office365</a> account, this was completely seamless. I simply entered my username and password, then within 20 seconds I had an inbox full of email! This, of course, is due to the wonders of the <a href="http://technet.microsoft.com/en-us/library/bb124251.aspx" target="_blank">AutoDiscover service</a> built in to Exchange 2010. But this isn’t the most exciting bit. When I decided to connect my corporate Exchange account, I was shown this little message:
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/03/image2.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border: 0px;" title="image" src="{{ site.baseurl }}/assets/image_thumb2.png" alt="image" width="637" height="149" border="0" /></a>
To me, this looks a lot like the native Mail client will support Exchange ActiveSync policies! Sure enough, when I logged into Exchange Control Panel and opened up the “Mobile Phones” panel, I was presented with this:
<a href="https://www.flamingkeys.com/wp-content/uploads/2012/03/image1.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border: 0px;" title="image" src="{{ site.baseurl }}/assets/image_thumb1.png" alt="image" width="651" height="203" border="0" /></a>
I’ve yet to spend any serious time testing this, but this is a pretty remarkable thing – Windows 8 is truly considered a mobile device rather than a regular MAPI client by the Exchange server. This certainly opens up some interesting questions. The native client is definitely not as versatile as Microsoft Outlook, but it’s more than functional enough as a mobile client. I can see myself using this full time on a slate device, then having Outlook on desktops. It also means that regular Joes in the company are able to have their corporate mail on their mobile Windows 8 device, whilst still having it controlled/monitored by their corporate IT! (I’ve not yet tested the wipe functionality. Will report back <img class="wlEmoticon wlEmoticon-winkingsmile" style="border-style: none;" src="{{ site.baseurl }}/assets/wlEmoticon-winkingsmile.png" alt="Winking smile" /> )
What do you think?
*Update: <a href="https://twitter.com/dmstork">Dave Stork</a> has dug a lot deeper than I <a href="http://blogs.dirteam.com/blogs/davestork/archive/2012/03/01/yes-there-is-activesync-in-windows-8.aspx">in his blog post</a> - go have a read!*
&nbsp;