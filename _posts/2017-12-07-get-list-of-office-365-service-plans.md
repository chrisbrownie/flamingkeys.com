---
layout: post
title: How to Export a List of Office 365 Service Plans
---

When licensing Office 365 users with Windows PowerShell, you'll potentially 
have a requirement to disable one or more options that comes with a 
particular license. For example, if a third-party email provider is in use,
you may need to disable Exchange Online. This is easy in the GUI, but 
challenging in PowerShell.

You'll use [New-MsolLicenseOptions](https://docs.microsoft.com/en-us/powershell/module/msonline/new-msollicenseoptions?view=azureadps-1.0) 
to create a `LicenseOptions` object that has all the components of your licence
configured, then apply that object to the users in question. The command looks
like this to assign an E3 license and disable Exchange Online:

{% gist a5c63e1d5bf167535418d5032e087cf4 %}    

That's all well and good, but how do you know it's `ENTERPRISEPACK` and 
`EXCHANGE_S_ENTERPRISE`? These are the internal identifiers for the E3 license 
and the Exchange Online service plan within the E3 license. They're challenging
(impossible?) to uncover in the GUI, but I've written a simple script to 
allow an administrator to retrieve the licenses and service plans in their
tenant. This should be run from the Microsoft Online Service PowerShell.

{% gist ab6ba3868618900cf4bbc46d960c5fee %}

This will output something like the following:

```
ServicePlan                AccountSkuId
-----------                ------------
BPOS_S_TODO_2              contoso:ENTERPRISEPACK
FORMS_PLAN_E3              contoso:ENTERPRISEPACK
STREAM_O365_E3             contoso:ENTERPRISEPACK
Deskless                   contoso:ENTERPRISEPACK
FLOW_O365_P2               contoso:ENTERPRISEPACK
POWERAPPS_O365_P2          contoso:ENTERPRISEPACK
TEAMS1                     contoso:ENTERPRISEPACK
PROJECTWORKMANAGEMENT      contoso:ENTERPRISEPACK
SWAY                       contoso:ENTERPRISEPACK
INTUNE_O365                contoso:ENTERPRISEPACK
YAMMER_ENTERPRISE          contoso:ENTERPRISEPACK
RMS_S_ENTERPRISE           contoso:ENTERPRISEPACK
OFFICESUBSCRIPTION         contoso:ENTERPRISEPACK
MCOSTANDARD                contoso:ENTERPRISEPACK
SHAREPOINTWAC              contoso:ENTERPRISEPACK
SHAREPOINTENTERPRISE       contoso:ENTERPRISEPACK
EXCHANGE_S_ENTERPRISE      contoso:ENTERPRISEPACK
BPOS_S_TODO_FIRSTLINE      contoso:DESKLESSPACK
FORMS_PLAN_K               contoso:DESKLESSPACK
STREAM_O365_K              contoso:DESKLESSPACK
FLOW_O365_S1               contoso:DESKLESSPACK
POWERAPPS_O365_S1          contoso:DESKLESSPACK
TEAMS1                     contoso:DESKLESSPACK
Deskless                   contoso:DESKLESSPACK
MCOIMP                     contoso:DESKLESSPACK
SHAREPOINTWAC              contoso:DESKLESSPACK
SWAY                       contoso:DESKLESSPACK
INTUNE_O365                contoso:DESKLESSPACK
YAMMER_ENTERPRISE          contoso:DESKLESSPACK
SHAREPOINTDESKLESS         contoso:DESKLESSPACK
EXCHANGE_S_DESKLESS        contoso:DESKLESSPACK
```

Here, `ENTERPRISEPACK` maps to an E3 license, and `DESKLESSPACK` maps to an F1 (formerly K1) license. As for what each service inside means? Well..you're on your own on that. They're often named somewhat sensibly (`YAMMER_ENTERPRISE`) and sometimes less so (`Deskless`).
