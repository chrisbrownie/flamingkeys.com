---
layout: post
title: Change Favicon in AD FS
---

A hallmark of any well branded web site is the favicon displayed in the browser tab. With a small amount of effort, we can apply a custom favicon to AD FS as well.

Firstly, take a look at this Gist from [Mathias Bynens](https://github.com/mathiasbynens). It demonstrates how to use JavaScript and HTML5 to change a page's favicon. As we can't touch the HTML in AD FS, this is what we need.

{% gist 428626 %}

# The procedure
First, you'll need a favicon.ico file. Mine is 32x32. Drop this on your AD FS server - we'll need this in a moment.

Add the following to your onload.js file:

{% gist 791160bc428b962666c58a8a34313886 %}

This will tell your visitors' browsers to look at _https://youradfsdomain/adfs/portal/logo/favicon.ico_ for this particular page's favicon. Now, let's upload the custom onload.js file and the favicon.ico file into the theme:

{% gist 7cf64414ada3e422e61bfd05d1aa2e91 %}
