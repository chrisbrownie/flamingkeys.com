---
layout: post
title: Office 365 Groups - Best Practices for Admins
---

Office 365 Groups are here! They have been for a while. They keep changing, nobody really knows what they do, and they kind of are just *there* . Sound familiar? Lots of Office 365 deployments leave the Groups configuration at the default...and that's kind of what Microsoft seems to want. Everything *Just Works* and your users are enabled to do whatever they want.

I think we can do it better, however. So in no particular order, here's my completely made up list of Best Practices for deploying Office 365 Groups. These are recommendations I've come up with after many Office 365 deployments involving Groups.

If you want to apply the settings I encourage below using Windows PowerShell, you'll likely need to grab a module, and a bit of configuration in advance. See *Configuring Windows PowerShell* at the bottom of this post.

## Don't turn off user creation of Groups

I know, I know, you're an AD person from the 2000s when the mantra was "users will break it if you let them touch it". Let's try something new: empower your users to do things within their own siloed areas they can't reach out of. If they break something in there, they're only breaking it for themselves and their colleagues, and probably only temporarily.

### Check if users are allowed to create Groups

```powershell
$Setting = Get-AzureADDirectorySetting  | Where {$_.DisplayName -eq "Group.Unified"}
$Setting['EnableGroupCreation']
```

### Enable users to create Groups

```powershell
$Setting = Get-AzureADDirectorySetting  | Where {$_.DisplayName -eq "Group.Unified"}
$Setting['EnableGroupCreation'] = $true
Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
```
## Group Naming Conventions

### Naming Prefixes

Naming prefixes allow for user created Groups to have distinguishing names to allow them to be identified as user-created. A popular choice is to prefix all Groups created by users with "GRP". This can be done as follows:

```powershell
$Setting = Get-AzureADDirectorySetting  | Where {$_.DisplayName -eq "Group.Unified"}
$Setting["PrefixSuffixNamingRequirement"] = "GRP_[GroupName]"
Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
```

### Banned words and profanity filtering

Empowering users is all well and good, but there's always the cheeky one or two who will think it's a great idea to put a profanity or other inappropriate word into a Group name. We can work to prevent this with banned words/profanity filtering.

```powershell
$Setting = Get-AzureADDirectorySetting  | Where {$_.DisplayName -eq "Group.Unified"}
$Setting["CustomBlockedWordsList"] = "Payroll,HR,Abuse,Complaints"
Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
```

## Group Email Address Conventions

Groups can live in any domain of your Office 365 tenant. By default, they'll use the default domain in your tenant. If you're running some kind of directory synchronization, this is probably your `.onmicrosoft.com` domain. Not exactly pretty, and entirely non-portable. I tend to find organizations are fond of the following two options:

* `@contoso.com`. Create an Email Address Policy for your Groups to place them under the same domain as your regular users. This allows for alignment and a consistent appearance irrespective of recipient type
* `@groups.contoso.com`. Create an Email Address Policy for your Groups to place them under their own domain. This requires some additional configuration in Office 365 to validate the domain, but it allows groups to exist separately under their own discrete namespace

Irrespective of which of the above you choose, do not leave it `@<tenant>.onmicrosoft.com`. The `onmicrosoft.com` domain belongs to Microsoft. If your organization chooses to leave Office 365 for another provider, any `@<tenant>.onmicrosoft.com` email addresses cannot be moved.

  New-EmailAddressPolicy -Name Groups -IncludeUnifiedGroupRecipients -EnabledEmailAddressTemplates "SMTP:@groups.contoso.com" -Priority 1

## Group Expiry

If you have Azure AD Premium P1 licensing or better, you can use Group expiry. This is a great way of encouraging your users to self-manage Groups. Group Owners receive an email when their Group is due to expire prompting them to renew it. If they don't renew it, the Group goes away.

* [Configure Group Expiry](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-groups-lifecycle-azure-portal)

## Configuring Windows PowerShell

To run the commands in this post, you'll need the following Windows PowerShell modules installed:

* **AzureADPreview** (The new module for managing Office 365 via the Graph. Exposes `*-AzureAD*` cmdlets). Note that you must be running the Preview version of this module to access all the right cmdlets. Several of these cmdlets collide with cmdlets exposed by the `AzureAD` module that you probably also have installed, so you should prefix any `*-AzureAD*` commands in this post with `AzureADPreview\`. So `Get-AzureADUser` would be issued as `AzureADPreview\Get-AzureADUser`

To install the modules above, run Windows PowerShell as an administrator and issue the following commands:

```powershell
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Install-Module AzureADPreview -AllowClobber
```

Once you've installed the module, connect to Azure AD. We'll make sure you already have a Unified Groups configuration setting to modify. If not, we'll create one.

```powershell
  AzureADPreview\Connect-AzureAD
  if (-not (Get-AzureADDirectorySetting | Where {$_.DisplayName -eq "Group.Unified"})) {
      # The setting doesn't exist, create it
      New-AzureADDirectorySetting -DirectorySetting (Get-AzureADDirectorySettingTemplate | Where-Object ({$_.DisplayName -eq "Group.Unified"}).CreateDirectorySetting()
  }
```

* **Exchange Online PowerShell** requires installation of a ClickOnce application from within Office 365. Instructions are on [TechNet](https://technet.microsoft.com/en-us/library/mt775114(v=exchg.160).aspx)

## Further Reading

* [Azure Active Directory cmdlets for configuring group settings](https://docs.microsoft.com/en-gb/azure/active-directory/active-directory-accessmanagement-groups-settings-cmdlets)
* [Office 365 Groups naming policy](https://support.office.com/en-us/article/office-365-groups-naming-policy-6ceca4d3-cad1-4532-9f0f-d469dfbbb552)