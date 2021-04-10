---
layout: post
title: Compare a file to a hash with PowerShell
---

This simple function allows a caller to compare a file to an existing hash in any format supported by [Get-FileHash](https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.utility/get-filehash). These, at time of writing (PowerShell 5.1 build 14393), are:

* SHA1
* SHA256
* SHA384
* SHA512
* MACTripleDES
* MD5
* RIPEMD160

Using the script is quite simple. Add it to your console or a script, then run:

```powershell
Compare-FileToHash -Path "C:\windows\system32\shell32.dll" -expectedHash "F683E63A08F385F52E86990CEB62CF1374A23373" -algorithm "SHA1"
```

This will return the full object that shows the expected, generated, and hashing result:

    Match Path                            ActualHash                               ExpectedHash
    ----- ----                            ----------                               ------------
     True C:\windows\system32\shell32.dll F683E63A08F385F52E86990CEB62CF1374A23373 F683E63A08F385F52E86990CEB62CF1374A23373

The result only can be retrieved by running the script as follows:

```powershell
(Compare-FileToHash -Path "C:\windows\system32\shell32.dll" -expectedHash "F683E63A08F385F52E86990CEB62CF1374A23373" -algorithm "SHA1").Match
```

This will return a boolean, either $true or $false.

    True

Easy!
