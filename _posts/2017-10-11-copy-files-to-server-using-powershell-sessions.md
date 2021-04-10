---
layout: post
title: Copying files to a server using PowerShell Sessions
---

In an environment with good security practice applied, you'll likely find you can't copy files to a secure server over RDP or via the usual methods:

* UNC to the server (`\\servername\c$\`)
* RDP via clipboard (`Ctrl`+`c` on your client, `Ctrl`+`v` on the server)
* RDP via redirected drive (`\\tsclient\c`)

Fortunately, WMF5 brings a new capability to PowerShell; the ability to copy files into and out of a PowerShell session. This is super easy to demonstrate:

```powershell
$session = New-PSSession -ComputerName MyServer
Copy-Item -Path "C:\myfile.txt" -Target "C:\SomeServerFolder" -ToSession $session
```

You can use the [`Copy-Item` cmdlet](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/copy-item?view=powershell-5.1) as per normal, just add the `-ToSession` parameter and pass in a PowerShell session. You'll even get a pretty progress bar if you decide to copy large or multiple files.
