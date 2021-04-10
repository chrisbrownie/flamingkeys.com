---
layout: post
title: Migrate/Replicate Photos from Office 365 to Active Directory
date: 2016-11-02 11:26:12.000000000 +11:00
type: post
published: true
status: publish
categories: []
tags: []
meta:
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  og:title: ''
  og:description: ''
  og:image: ''
  _yoast_wpseo_primary_category: ''
  _yoast_wpseo_content_score: '30'
  dsq_thread_id: '5272467087'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

Users can easily set their photos in Office 365 via any number of interfaces. Performing this change in AD is a little more challenging. Fortunately, we can quite easily write these photos back into Active Directory with a little PowerShell script. You'll need the ActiveDirectory module and Exchange Online credentials with any multi-factor *disabled* as Exchange Web Services is used, which does not presently support MFA.

```powershell
function Get-Office365Photo($EmailAddress,$Credential) {
    $wc = New-Object System.Net.WebClient
    $wc.credentials = $Credential
    # Build the URL that'll return the jpeg of the user's photo
    $url = "https://outlook.office365.com/ews/exchange.asmx/s/GetUserPhoto?email=$EmailAddress&size=HR96x96"
    # Build a path to export it to (.\kim.akers@contoso.com.jpg)
    $outPath = "$pwd\$EmailAddress.jpg"
    try { 
        # Download the image and save it to the current directory
        $wc.DownloadFile($url,$outPath)
        return $outPath
    } catch { throw $_ }
}

function Upload-ADPhoto($Username,$FilePath) {
    # Import the photo into a variable as a byte array
    $photo = [byte[]](Get-Content $FilePath -Encoding byte)
    # Replace the current value of thumbnailPhoto with the byte array from above
    Set-ADUser $Username -Replace @{ThumbnailPhoto=$photo}
}

# Get the credential to allow us to download the images
$Cred = Get-Credential -Message "Please enter your Office 365 Credentials"

# Get every mail-enabled AD user
$users = Get-ADUser -ldapfilter '(mail=*)' -properties mail

# For each of the mail-enabled users...
foreach ($user in $users) {
    try {
        # Download the photo
        $photoPath = Get-Office365Photo -EmailAddress $user.mail -Credential $Cred
        # Upload the photo
        Upload-ADPhoto -Username $user -FilePath $photoPath
    } catch {
        Write-Warning "Unable to update image for $($user.mail)"
    }
}
```