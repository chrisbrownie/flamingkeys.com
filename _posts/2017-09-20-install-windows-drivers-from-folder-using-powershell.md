---
layout: post
title: Install Windows Drivers from a Folder Using PowerShell
---

When building any computer, virtual or physical, it requires drivers. This script will allow you to place a folder of `.inf` (and associated) files onto a computer and then install all relevant drivers. This script is tested as working on Windows Server 2016 and Windows Server 2016 Core.

```powershell
Get-ChildItem "C:\mydrivers\" -Recurse -Filter "*.inf" | 
ForEach-Object { PNPUtil.exe /add-driver $_.FullName /install }
```

## How does it work?

The script is very simple:

1. `Get-ChildItem` retrieves any files with a `.inf` extension in the `C:\mydrivers\` directory and its subfolders
2. `ForEach-Object` loops over these and performs the commands in the script block `{ }` against them
3. `PNPUtil.exe` is a CLI utility for management of the Windows driver store. The `/add-driver` argument adds the specified driver to the driver store. `/install` installs the driver, resolving any missing driver issues for any attached hardware the driver suits
