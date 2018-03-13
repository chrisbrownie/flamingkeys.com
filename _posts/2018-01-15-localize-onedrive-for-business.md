---
layout: post
title: Localized(/localised) OneDrive For All
---

Office Apps in the browser (Excel Online/Word Online/PowerPoint Online/etc.) all utilise a user's OneDrive Site Collection to gather their settings and configuration. If your users are somewhere other than the U.S., they probably use a sensible date format like dd/mm/yyyy or dd/mm/yy. None of this backwards mm/dd/yyyy stuff.

Users can change their own OneDrive locale in Site Collection Settings...but try telling that to 20,000 users. Along comes [@brendankarl](https://blogs.technet.microsoft.com/fromthefield/2015/04/13/office-365-change-the-locale-of-all-onedrive-for-business-sites-using-powershell/). This article provides a good start to getting this done, but it's still a bit manual for my liking. Here's my adaptation of Brendan's script.

Firstly, you'll need an account that's a SharePoint Administrator or more. Chances are you're a Global Admins member, so we'll roll under that assumption. Additionally, you'll want the `Microsoft.SharePointOnline.CSOM` NuGet package, or grab the redistributable version.

Let's assume we're working on `contoso.onmicrosoft.com`.

There's two scripts to run. The first will make you an Admin level user on every single OneDrive for Business in the tenant. Sounds nasty, and it kind of is. Make sure this isn't going to get you fired.

```ps
GrantPermissionsForOneDriveOwnership.ps1 `
    -TenantServiceDomain contoso `
    -Credentials (Get-Credential admin@contoso.onmicrosoft.com)
```

Great, now we own everyone's OneDrive for Business Sites, let's set the new locale ID. Grab the ID number from [here](https://msdn.microsoft.com/en-us/library/ms912047%28v=winembedded.10%29.aspx?f=255&MSPPError=-2147217396). We'll set our Contoso users to utilise the *English (Australia)* locale, otherwise known as `3081`.

```ps
LocalizeOneDriveForBusiness.ps1 `
    -NewLocaleId 1234 `
    -TenantServiceDomain contoso `
    -Credentials (Get-Credential)
```

Easy as that! If you have 20,000 users, this will take a very long time.
