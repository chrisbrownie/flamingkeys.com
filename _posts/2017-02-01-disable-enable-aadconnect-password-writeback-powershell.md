---
layout: post
title: How to Disable and Enable AAD Connect Password Writeback using PowerShell
---

Disabling AAD Connect Password Writeback is easy in both the GUI and Windows PowerShell. In a recent case I found myself troubleshooting AAD Connect where it was in a very broken state that meant the GUI was unavailable due to a pending upgrade:

![](https://static.flamingkeys.com/wp-content/uploads/2017/02/01223644/aadconnect-upgrade-blocker1.png)

As part of my troubleshooting, I determined that Password Writeback needed to be disabled. Unfortunately, the dialogue shown above is blocking, which means I was unable to switch it off using the regular GUI. In comes PowerShell:

# Disable Password Sync

To disable AAD Connect Password Writeback using Windows PowerShell, run the following commands from a shell as administrator on your AAD Connect server:

```powershell
Import-Module ADSync
$connector = (Get-ADSyncConnector | Where-Object {$_.Name -ilike "*AAD"}).Name
Get-ADSyncAADPasswordResetConfiguration -Connector $connector
Set-ADSyncAADPasswordResetConfiguration -Connector $connector -Enable:$false
```

# Enable Password Sync

To enable AAD Connect Password Writeback using Windows Powershell, run the following commands from a shell as administrator on your AAD Connect server:

```powershell
Import-Module ADSync
$connector = (Get-ADSyncConnector | Where-Object {$_.Name -ilike "*AAD"}).Name
Get-ADSyncAADPasswordResetConfiguration -Connector $connector
Set-ADSyncAADPasswordResetConfiguration -Connector $connector -Enable:$true 
```