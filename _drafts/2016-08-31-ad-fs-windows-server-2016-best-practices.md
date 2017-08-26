---
layout: post
title: AD FS for Windows Server 2016 Best Practices
date: 2016-08-31 00:00:19.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD FS
- Office 365
- PowerShell
- Security
- Server 2016
- windows
- Windows Server 2016
meta:
  _yoast_wpseo_content_score: '30'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _yoast_wpseo_primary_category: '8'
  _yoast_wpseo_focuskw: AD FS
  _yoast_wpseo_focuskw_text_input: AD FS
  _yoast_wpseo_linkdex: '72'
  dsq_thread_id: '5108061256'
  amazonS3_cache: a:7:{s:98:"//flamingkeys.com/wp-content/uploads/2016/08/2016-08-31-16_50_36-MT-FS16-01-VMware-Workstation.png";i:1177;s:114:"//static.flamingkeys.com/wp-content/uploads/2016/08/31065112/2016-08-31-16_50_36-MT-FS16-01-VMware-Workstation.png";i:1177;s:71:"//flamingkeys.com/wp-content/uploads/2016/08/adfs2016-claimissuance.png";i:1179;s:87:"//static.flamingkeys.com/wp-content/uploads/2016/08/31071355/adfs2016-claimissuance.png";i:1179;s:73:"//flamingkeys.com/wp-content/uploads/2016/08/adfs2016-claimsissuance2.png";i:1187;s:89:"//static.flamingkeys.com/wp-content/uploads/2016/08/31072552/adfs2016-claimsissuance2.png";i:1187;s:93:"//blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx";a:1:{s:9:"timestamp";i:1496629680;}}
  _yst_is_cornerstone: ''
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
Active Directory Federation Service has come a long way since humble beginnings in Server 2003 with AD FS 1.0. Now availabile on Windows Server 2016, Microsoft have taken big steps to allow for customization and versatility of the product. There's a lot you can change, and I'll attempt to summarise my list of recommended changes below. This is by no means an exhaustive list, but it's a good place to start for any new deployment.
If you're using Windows Server 2012 R2, you'll want the <a href="https://flamingkeys.com/2015/06/ad-fs-3-best-practices/" target="_blank">AD FS 3 Best Practices</a> post.
<h4>Ensure AD FS Farm Behavior Level is set to the highest possible level</h4>
Much like Active Directory Domain Services, AD FS now has the concept of a "functional level". The Farm Behavior Level (FBL) is an integer that dictates the farm's functional level. You can view the current FBL by running:
{% highlight powershell %}
Get-AdfsProperties | Select CurrentFarmBehavior
{% endhighlight %}
A value of 1 indicates the Windows Server 2012 R2 FBL, and 3 indicates Windows Server 2016.
If you have at least one Windows Server 2016 Domain Controller, or have run the Windows Server 2016 AD Prep, and have no Windows Server 2012 R2 federation servers, run the following command to raise the FBL to Windows Server 2016:
{% highlight powershell %}
Invoke-AdfsFarmBehaviorLevelRaise
{% endhighlight %}
Enter "Y" when prompted, and a few minutes later, AD FS will be upgraded to Windows Server 2016.
Validate this using Get-AdfsProperties again:
{% highlight powershell %}
Get-AdfsProperties | Select CurrentBehaviorLevel
{% endhighlight %}
<h4>Enable Keep Me Signed In (KMSI)</h4>
This will allow your users to receive cookies that last longer than the single session. I've written a step-by-step <a href="https://flamingkeys.com/2015/06/enable-keep-me-signed-in-for-ad-fs-3-0/" target="_blank">here</a>.
<h4>**Enable End User Password Change**</h4>
AD FS has always had a "Change Password" endpoint available, but it's turned off by default. Run the following commands to enable the endpoint:
{% highlight powershell %}
Enable-AdfsEndpoint "/adfs/portal/updatepassword/"
Set-AdfsEndpoint "/adfs/portal/updatepassword/" -Proxy:$true
Restart-Service AdfsSrv -Force
{% endhighlight %}
If you have more than one AD FS server, you'll need to restart the service on all of them.
<h4>Enable WS-Trust 1.3 for Desktop Client SSO</h4>
Microsoft are <a href="https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/" target="_blank">rolling out</a> ADAL (Active Directory Authentication Library) authentication to Office 2013/2016/ProPlus, and enabling WS-Trust 1.3 will provide you with SSO for this newly enabled technology. Run the following PowerShell command to enable the endpoint for WS-Trust 1.3:
{% highlight powershell %}
Enable-AdfsEndpoint -TargetAddressPath "/adfs/services/trust/13/windowstransport"
{% endhighlight %}
You'll also want to ensure you have both Forms and Windows Authentication (WIA) enabled in your global authentication policies.
<img class="aligncenter size-full wp-image-1177" src="{{ site.baseurl }}/assets/2016-08-31-16_50_36-MT-FS16-01-VMware-Workstation.png" alt="2016-08-31 16_50_36-MT-FS16-01 - VMware Workstation" width="486" height="543" />*source: <a href="http://blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx" target="_blank">http://blogs.msdn.com/b/samueld/archive/2015/06/05/office-modern-auth-amp-adfs-making-it-work.aspx</a>*
<h4>Enable Password Expiry Notification in Office 365</h4>
Office 365 has functionality built in that will prompt users when their password is about to expire. This works well, provided it...you know...knows. We can provide it this information by way of a SAML claim.
Add the following Issuance Transform Rule to your Office 365 relying party trust:
<pre>c1:[Type == "http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime"]
=> issue(store = "_PasswordExpiryStore", types = ("http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime", "http://schemas.microsoft.com/ws/2012/01/passwordexpirationdays", "http://schemas.microsoft.com/ws/2012/01/passwordchangeurl"), query = "{0};", param = c1.Value);
</pre>
You can do this easily by running the following PowerShell:
{% highlight powershell %}
$msolId = "urn:federation:MicrosoftOnline"
$rptName = "Microsoft Office 365 Identity Platform"
$rptRules = (Get-AdfsRelyingPartyTrust -Identifier $msolId).IssuanceTransformRules
$newRule =  '@RuleTemplate = "LdapClaims" @RuleName = "UPN Claim Rule" c1:[Type == "http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime"] => issue(store = "_PasswordExpiryStore", types = ("http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime","http://schemas.microsoft.com/ws/2012/01/passwordexpirationdays","http://schemas.microsoft.com/ws/2012/01/passwordchangeurl"), query = "{0};", param = c1.Value);'
$rptRules = $rptRules + $newRule
Set-AdfsRelyingPartyTrust -TargetName $rptName -IssuanceTransformRules $rptRules
{% endhighlight %}
<h4>Enable Authentication Methods References Claim Rule</h4>
In a default configuration, if a user is enabled for MFA both on-premises (e.g. x509 certificate or Duo connected to AD FS), *and* is enabled for MFA in Azure AD, they'll be prompted to authenticate twice. While this might be seen as "acceptable" by the definition of multifactor authentication (username, password, cert/duo, phone), it's likely considered overkill in most applications. We can configure an AD FS claim to tell Azure AD whether MFA has been performed or not on-premises quite easily.
<ul>
<li>Right-click the Microsoft Office 365 Identity Platform relying party and choose *Edit Claim Issuance Policy...*
<img class="aligncenter size-full wp-image-1179" src="{{ site.baseurl }}/assets/adfs2016-claimissuance.png" alt="adfs2016-claimissuance" width="465" height="188" /></li>
<li>On the Issuance Transform Rules tab, choose *Add Rule...*</li>
<li>Select *Pass through or Filter an Incoming Claim*</li>
<li>Configure the rule as follows:<img src="{{ site.baseurl }}/assets/adfs2016-claimsissuance2.png" alt="adfs2016-claimsissuance2" width="716" height="582" class="aligncenter size-full wp-image-1187" /></li>
</ul>
Now the following claim will be sent to Azure AD, informing it, if appropriate, that MFA has already been achieved on premises:
<pre><saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
</saml:Attribute>
</pre>
<h4>Enable Extranet Lockout</h4>
Utilising <a href="https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-fs/operations/best-practices-securing-ad-fs#extranet-soft-lockout-protection-for-accounts" target="_blank">AD FS Extranet Lockout</a> significantly enhances the protections provided by your Web Application Proxy server(s). Functioning entirely independently of AD password policies, this provides an element of DoS protection with minimal effort.
{% highlight powershell %}
Get-AdfsProperties | Format-List ExtranetLockoutEnabled,ExtranetLockoutThreshold,ExtranetObservationWindow,ExtranetLockoutRequirePDC
{% endhighlight %}
The four returned properties pertain to Extranet Lockout.
**ExtranetLockoutEnabled** - does what it says on the box. If set to true, Extranet Lockout is turned on.
**ExtranetLockoutThreshold** - the number of sequential failed logins to permit before locking the user out. Keep in mind that every failed AD FS logon equals a failed Active Directory logon, so it makes sense to have this value set *lower* than your AD lockout threshold.
**ExtranetObservationWindow** - this is a TimeSpan object that defines how long AD FS will lock a user out for after.
**ExtranetLockoutRequirePDC** - this is a new one in Server 2016. In previous versions of AD FS, Extranet Lockout checked the PDC emulator to determine the number of failed login attempts for a particular account. By changing this setting to false, we can instruct AD FS to contact any DC if the PDC emulator is unavailable, greatly improving the stability of Extranet Lockout (previously, if the PDC emulator was unavailable and Extranet Lockout was enabled, authentications would fail).
The following command will configure Extranet Lockout protection to lock a user out for 10 minutes after 15 failed logins, and will allow the failed login count to be retrieved from a DC other than the PDC emulator:
{% highlight powershell %}
Set-AdfsProperties -EnableExtranetLockout:$true -ExtranetLockoutThreshold 15 -ExtranetObservationWindow (New-TimeSpan -Minutes 10) -ExtranetLockoutRequirePDC $false
{% endhighlight %}
<h4>Prevent non-domain joined computers getting the Basic Authentication 401 prompt</h4>
This is one that I didn't even realise could be fixed until I stumbled upon <a href="http://blog.kloud.com.au/2014/11/06/implementing-adfs-v3-0-forms-authentication-in-mixed-environments/" target="_blank">this</a> really clever solution from Mark Southwell over at <a href="http://www.kloud.com.au" target="_blank">Kloud</a>. Basically you introduce a special user agent for your domain joined machines, then only issue 401s to browsers presenting that user agent. I have used this setting in the past to enable WIA for non-IE browsers, but this is a whole new way of using the functionality. Check out Mark's post for specifics: <a href="http://blog.kloud.com.au/2014/11/06/implementing-adfs-v3-0-forms-authentication-in-mixed-environments/" target="_blank">Implementing ADFS V3.0 Forms Authentication in Mixed Environments</a>. Be sure to check out the bottom comment by Robet Carsey as well: this looks like an even better solution.
<h4>Extend lifetimes for Token-Signing and Token-Decrypting certificates</h4>
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
<h4>Enable sensible logging</h4>
AD FS doesn't log authentication successes or failures by default; these need to be turned on:
{% highlight powershell %}
Set-ADFSProperties –LogLevel Information,Errors,Verbose,Warnings,FailureAudits,SuccessAudits
{% endhighlight %}
Then use Group Policy or the following command to enable Success and Failure audit logging:
[text]
auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable
[/text]
<h4>Keep on top of patches</h4>
As with everything Windows, it's important to keep up to date with patching. The directory team have been nice to us with AD FS: there's a TechNet Library page dedicated to this very purpose:
<a href="https://docs.microsoft.com/en-au/windows-server/identity/ad-fs/deployment/updates-for-active-directory-federation-services-ad-fs">Updates for Active Directory Federation Services (AD FS)</a>.
