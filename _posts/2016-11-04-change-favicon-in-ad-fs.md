---
layout: post
title: Change Favicon in AD FS
---

A hallmark of any well branded web site is the favicon displayed in the browser tab. With a small amount of effort, we can apply a custom favicon to AD FS as well.

Firstly, take a look at [this Gist](https://gist.github.com/mathiasbynens/428626) from [Mathias Bynens](https://github.com/mathiasbynens). It demonstrates how to use JavaScript and HTML5 to change a page's favicon. As we can't touch the HTML in AD FS, this is what we need.

```javascript
/*!
 * Dynamically changing favicons with JavaScript
 * Works in all A-grade browsers except Safari and Internet Explorer
 * Demo: http://mathiasbynens.be/demo/dynamic-favicons
 */

// HTML5™, baby! http://mathiasbynens.be/notes/document-head
document.head || (document.head = document.getElementsByTagName('head')[0]);

function changeFavicon(src) {
 var link = document.createElement('link'),
     oldLink = document.getElementById('dynamic-favicon');
 link.id = 'dynamic-favicon';
 link.rel = 'shortcut icon';
 link.href = src;
 if (oldLink) {
  document.head.removeChild(oldLink);
 }
 document.head.appendChild(link);
}
```

# The procedure
First, you'll need a favicon.ico file. Mine is 32x32. Drop this on your AD FS server - we'll need this in a moment.

Add the following to your onload.js file:

```powershell
// From https://gist.github.com/mathiasbynens/428626

document.head || (document.head = document.getElementsByTagName('head')[0]);
function changeFavicon(src) {
    var link = document.createElement('link'),
        oldLink = document.getElementById('dynamic-favicon');
    link.id = 'dynamic-favicon';
    link.rel = 'icon';
    link.href = src;
    if (oldLink) {
     document.head.removeChild(oldLink);
    }
    document.head.appendChild(link);
   }

changeFavicon('/adfs/portal/logo/favicon.ico');
```

This will tell your visitors' browsers to look at _https://youradfsdomain/adfs/portal/logo/favicon.ico_ for this particular page's favicon. Now, let's upload the custom onload.js file and the favicon.ico file into the theme:

```powershell
Set-AdfsWebTheme -TargetName "yourcustomtheme" -AdditionalFileResource 
    @{
        Uri="/adfs/portal/script/onload.js"
        Path="C:\adfs\assets\theme\script\onload.js"
    }, @{
        Uri="/adfs/portal/logo/favicon.ico"
        Path="C:\adfs\assets\script\onload.js"
    }
```
