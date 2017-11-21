---
layout: post
title: AD FS 3 Best Practices from the Field
date: 2015-06-25 12:47:38.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD FS
- Microsoft
- PowerShell
- Server 2012 R2
- windows
meta:
  og:image: ''
  og:description: ''
  og:title: ''
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  dsq_thread_id: '3877957274'
  _yoast_wpseo_content_score: '60'
  _yoast_wpseo_primary_category: ''
  amazonS3_cache: a:9:{s:68:"//flamingkeys.com/wp-content/uploads/2015/06/office365claimrule1.png";i:1169;s:84:"//static.flamingkeys.com/wp-content/uploads/2015/06/31012623/office365claimrule1.png";i:1169;s:68:"//flamingkeys.com/wp-content/uploads/2015/06/office365claimrule2.png";i:1170;s:84:"//static.flamingkeys.com/wp-content/uploads/2015/06/31012635/office365claimrule2.png";i:1170;s:93:"//blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx";a:1:{s:9:"timestamp";i:1476334717;}s:51:"//technet.microsoft.com/en-us/library/dn486806.aspx";a:1:{s:9:"timestamp";i:1476334717;}s:51:"//technet.microsoft.com/en-us/library/mt126278.aspx";a:1:{s:9:"timestamp";i:1476334717;}s:52:"//flamingkeys.com/wp-content/uploads/2015/06/gap.jpg";i:1028;s:123:"//flamingkeys.com/wp-content/uploads/2015/06/2015-06-25-22_46_23-Updates-for-Active-Directory-Federation-Services-AD-FS.png";i:1033;}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /adfsbp
  - /adfsbp/
  - /2015/06/ad-fs-3-best-practices/
---
Active Directory Federation Service has come a long way since humble beginnings in Server 2003 with AD FS 1.0\. Now at version 3.0 on Windows Server 2012 R2, Microsoft have taken big steps to allow for customisation and versatility of the product. There's a lot you can change, and I'll attempt to summarise my list of recommended changes below. This is by no means an exhaustive list, but it's a good place to start for any new deployment. Running Windows Server 2016? You probably want [AD FS for Windows Server 2016 Best Practices](/ad-fs-windows-server-2016-best-practices/).

## Enable Keep Me Signed In (KMSI)

This will allow your users to receive cookies that last longer than the single session. I've written a step-by-step [here](/enable-keep-me-signed-in-for-ad-fs-3-0/).

## Enable End User Password Change

AD FS 3 has always had a "Change Password" endpoint available, but it's turned off by default, and unless you install KB3035025, you must be using a workplace-joined device to access this functionality. Step 1: Install [KB3035025](https://support.microsoft.com/en-us/kb/3035025) on all your AD FS servers Step 2: Run the following commands to enable the endpoint: 
{% highlight powershell %}
Enable-AdfsEndpoint "/adfs/portal/updatepassword/" Set-AdfsEndpoint "/adfs/portal/updatepassword/" -Proxy:$true Restart-Service AdfsSrv -Force 
{% endhighlight %}

## Enable WS-Trust 1.3 for Desktop Client SSO

Microsoft are [rolling out](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) ADAL (Active Directory Authentication Library) authentication to Office 2013, and enabling WS-Trust 1.3 will provide you with SSO for this newly enabled technology. Run the following PowerShell command to enable the endpoint for WS-Trust 1.3: 

{% highlight powershell %} 
Enable-AdfsEndpoint -TargetAddressPath "/adfs/services/trust/13/windowstransport" 
{% endhighlight %} 

You'll also want to ensure you have both Forms and Windows Authentication (WIA) enabled in your global authentication policies. 

![gap]({{ site.baseurl }}/assets/gap.jpg)

*source: [http://blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx](http://blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx)*

## Enable Password Expiry Notification in Office 365

Office 365 has functionality built in that will prompt users when their password is about to expire. This works well, provided it...you know...knows. We can provide it this information by way of a SAML claim. Add the following Issuance Transform Rule to your Office 365 relying party trust:

    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime"]
    => issue(store = "_PasswordExpiryStore", types = ("http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime", "http://schemas.microsoft.com/ws/2012/01/passwordexpirationdays", "http://schemas.microsoft.com/ws/2012/01/passwordchangeurl"), query = "{0};", param = c1.Value);


You can do this easily by running the following PowerShell: 

{% highlight powershell %} 
$msolId = "urn:federation:MicrosoftOnline" 
$rptName = "Microsoft Office 365 Identity Platform" 
$rptRules = (Get-AdfsRelyingPartyTrust -Identifier $msolId).IssuanceTransformRules 
$newRule = '@RuleTemplate = "LdapClaims" @RuleName = "UPN Claim Rule" c1:[Type == "http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime"] => issue(store = "_PasswordExpiryStore", types = ("http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime","http://schemas.microsoft.com/ws/2012/01/passwordexpirationdays","http://schemas.microsoft.com/ws/2012/01/passwordchangeurl"), query = "{0};", param = c1.Value);' 
$rptRules = $rptRules + $newRule 
Set-AdfsRelyingPartyTrust -TargetName $rptName -IssuanceTransformRules $rptRules 
{% endhighlight %}

## Enable Authentication Methods References Claim Rule

In a default configuration, if a user is enabled for MFA both on-premises (e.g. x509 certificate or Duo connected to AD FS), *and* is enabled for MFA in Azure AD, they'll be prompted to authenticate twice. While this might be seen as "acceptable" by the definition of multifactor authentication (username, password, cert/duo, phone), it's likely considered overkill in most applications. We can configure an AD FS claim to tell Azure AD whether MFA has been performed or not on-premises quite easily.

*   Right-click the Microsoft Office 365 Identity Platform relying party and choose *Edit Claims Rules...* ![office365claimrule1]({{ site.baseurl }}/assets/office365claimrule1.png)
*   Select *Pass through or Filter an Incoming Claim*
*   On the Issuance Transform Rules tab, choose *Add Rule...*
*   Configure the rule as follows: ![office365claimrule2]({{ site.baseurl }}/assets/office365claimrule2.png)

Now the following claim will be sent to Azure AD, informing it, if appropriate, that MFA has already been achieved on premises:

    <saml:attribute attributenamespace="http://schemas.microsoft.com/claims" attributename="authnmethodsreferences"><saml:attributevalue>http://schemas.microsoft.com/claims/multipleauthn</saml:attributevalue></saml:attribute> 

## Enable Extranet Lockout

Utilising [AD FS Extranet Lockout](https://technet.microsoft.com/en-us/library/dn486806.aspx) significantly enhances the protections provided by your Web Application Proxy server(s). Functioning entirely independently of AD password policies, this provides an element of DDoS protection with minimal effort. 

{% highlight powershell %} 
Get-AdfsProperties | Format-List ExtranetLockoutEnabled,ExtranetLockoutThreshold,ExtranetObservationWindow 
{% endhighlight %} 

The three returned properties pertain to Extranet Lockout.
| Property | Functionality
| --- | ---
| `ExtranetLockoutEnabled` | Does what it says on the box. If set to true, Extranet Lockout is turned on
| `ExtranetLockoutThreshold` | The number of sequential failed logins to permit before locking the user out. Keep in mind that every failed AD FS logon equals a failed Active Directory logon, so it makes sense to have this value set *lower* than your AD lockout threshold
| `ExtranetObservationWindow` | This is a TimeSpan object that defines how long AD FS will lock a user out for after

The following command will configure Extranet Lockout protection to lock a user out for 10 minutes after 15 failed logins: 

{% highlight powershell %} 
Set-AdfsProperties -EnableExtranetLockout:$true -ExtranetLockoutThreshold 15 -ExtranetObservationWindow (New-TimeSpan -Minutes 10) 
{% endhighlight %}

## Prevent non-domain joined computers getting the Basic Authentication 401 prompt

This is one that I didn't even realise could be fixed until I stumbled upon [this](http://blog.kloud.com.au/2014/11/06/implementing-adfs-v3-0-forms-authentication-in-mixed-environments/) really clever solution from Mark Southwell over at [Kloud](http://www.kloud.com.au). Basically you introduce a special user agent for your domain joined machines, then only issue 401s to browsers presenting that user agent. I have used this setting in the past to enable WIA for non-IE browsers, but this is a whole new way of using the functionality. Check out Mark's post for specifics: [Implementing ADFS V3.0 Forms Authentication in Mixed Environments](http://blog.kloud.com.au/2014/11/06/implementing-adfs-v3-0-forms-authentication-in-mixed-environments/). Be sure to check out the bottom comment by Robet Carsey as well: this looks like an even better solution.

## Extend lifetimes for Token-Signing and Token-Decrypting certificates

One of an AD FS admin's least favourite tasks has to be updating certificates. These need to be timed well, and planned far in advance. You can reduce the pain of this significantly by increasing the lifetime of your token-signing and token-decrypting certificates. The default validity period for these certs in AD FS is one year. I typically will increase this to 5 years. Check your current validity first:

{% highlight powershell %}
Get-AdfsProperties | Select CertificateDuration
{% endhighlight %}

Change this to a new value (5 years, here) and then regenerate the certificates. Keep in mind, this method will break existing relationships, so only perform it on new servers or during a change window!

{% highlight powershell %}
Set-AdfsProperties -Certificateduration 1827
Update-AdfsCertificate -CertificateType Token-Decrypting -Urgent
Update-AdfsCertificate -CertificateType Token-Signing -Urgent
{% endhighlight %}

## Apply modern branding

Microsoft have rebranded the Microsoft Online login pages to be more _modern_. Consider updating AD FS to use this branding. There's a good guide [here](https://github.com/Microsoft/adfsWebCustomization/tree/master/centeredUi) on Microsoft's GitHub.

## Enable sensible logging

AD FS doesn't log authentication successes or failures by default; these need to be turned on:

{% highlight powershell %}
Set-ADFSProperties –LogLevel Information,Errors,Verbose,Warnings,FailureAudits,SuccessAudits
{% endhighlight %}

Then use Group Policy or the following command to enable Success and Failure audit logging:

    auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable

## Keep on top of patches

As with everything Windows, it's important to keep up to date with patching. The directory team have been nice to us with AD FS: there's a TechNet Library page dedicated to this very purpose: 
[Updates for Active Directory Federation Services (AD FS)](https://technet.microsoft.com/en-us/library/mt126278.aspx). Keep an eye on this page for updates for all supported versions of AD FS: 

![AD FS Updates]({{ site.baseurl }}/assets/2015-06-25-22_46_23-Updates-for-Active-Directory-Federation-Services-AD-FS.png)
