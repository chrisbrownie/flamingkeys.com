---
layout: post
title: Moving the Mouse Cursor with Windows PowerShell
date: 2011-03-03 08:37:22.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Annoyances
- Computers
- Group Policy
- Microsoft
- PowerShell
- Scripting
- tips
meta:
  _edit_last: '1'
  robotsmeta: index,follow
  _sexybookmarks_shortUrl: http://fla.im/eUOhBD
  _wp_old_slug: ''
  _sexybookmarks_permaHash: 3c2c8c85a06fea7f21efb17b79c749d3
  dsq_thread_id: '244150338'
  _jd_tweet_this: ''
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
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2011/03/moving-the-mouse-cursor-with-windows-powershell/
---

We have several large displays with some rudimentary presentation software on them (displaying RSS feeds, video stream etc). Unfortunately this software is..not ideally written... For this reason I have published out a script to have the computers restart every morning at 5AM. Unfortunately, as a result of this reboot, we end up with the mouse cursor smack bang in the middle of the screen. In my discussions with one of our HelpDesk staff we concluded that a small script to move the mouse out of the way would be a far more ideal use of everyone's time than for him to meander on over and move the mouse himself. Moving the mouse cursor with PowerShell is much easier than one may imagine.

First, load the .Net class System.Windows.Forms with the following command:

{% highlight powershell %}
Add-Type -AssemblyName System.Windows.Forms
{% endhighlight %}

Secondly, let's create an object that contains the dimensions of our screen. Because the "active corner" of the mouse cursor is the far top left pixel of the cursor, we can obscure all but this single pixel by placing it in the bottom right corner of the window, that is, x-max and y-max of the screen resolution. We'll use the `SystemInformation` member `VirtualScreen` for this:

{% highlight powershell %}
$screen = [System.Windows.Forms.SystemInformation]::VirtualScreen
{% endhighlight %}

By running a Get-Member over this new variable we can get a list of all the properties that are members of this VirtualScreen:

{% highlight powershell %}
$screen | Get-Member -MemberType Property
{% endhighlight %}

    TypeName: System.Drawing.Rectangle
    Name     MemberType Definition
    ----     ---------- ----------
    Bottom   Property   System.Int32 Bottom {get;}
    Height   Property   System.Int32 Height {get;set;}
    IsEmpty  Property   System.Boolean IsEmpty {get;}
    Left     Property   System.Int32 Left {get;}
    Location Property   System.Drawing.Point Location {get;set;}
    Right    Property   System.Int32 Right {get;}
    Size     Property   System.Drawing.Size Size {get;set;}
    Top      Property   System.Int32 Top {get;}
    Width    Property   System.Int32 Width {get;set;}
    X        Property   System.Int32 X {get;set;}
    Y        Property   System.Int32 Y {get;set;}

The properties here that are relevant are `Width` and `Height` (These are the two far-most points at the right-hand size and bottom of the display. Windows considers the origin of the screen (that is; coordinates (0,0)) to be the very top-left pixel of the screen. You can see here the results for my 1080p display:

    PS> $screen.Width
    1920
    PS> $screen.Height
    1080

After running this script, the mouse cursor will be at coordinates `(1920,1080)`

Finally, let's change the cursor position to these variables:

{% highlight powershell %}
[Windows.Forms.Cursor]::Position = "$($screen.Width),$($screen.Height)"
{% endhighlight %}

Simply hit go and watch your mouse cursor whiz away to the corner!
Three lines of code takes about as much time to write and deploy as it would for the HelpDesk staff member in question to go and move the mouse on the displays.
Scripting: 1. Humanity: 0.
