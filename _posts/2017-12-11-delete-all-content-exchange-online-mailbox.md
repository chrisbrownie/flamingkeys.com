---
layout: post
title: Delete All Content from an Exchange Online Mailbox
---

Exchange has traditionally offered several methods by which to delete content 
from a mailbox as an administrator. `Search-Mailbox` is good, but runs 
synchronously and requires that PowerShell be running, connected to Exchange 
Online for the duration. Compliance Searches can work too, but in my experience,
they're slow and unreliable.

The most reliable method I've found so far is to create a retention policy with
a one-day deletion tag, then force the Managed Folder Assistant to process the
mailbox. This looks like the following:

## Create a retention tag

This creates a tag called *1 Day Delete* which deletes items more than 1 day 
old

```powershell
New-RetentionPolicyTag -Name "1 Day Delete" -AgeLimitForRetention (New-TimeSpan -Days 1) -RetentionAction DeleteAndAllowRecovery -Type "All"
```

## Create a retention policy and assign the 1 Day Delete Tag

This creates a policy called *Mailbox Purge Policy* and assigns the 
`1 Day Delete` tag to it.

```powershell
New-RetentionPolicy -Name "Mailbox Purge Policy" -RetentionPolicyTagLinks "1 Day Delete"
```

## Assign the retention policy to the mailboxes to be purged

Let's assign the *Mailbox Purge Policy* policy to the mailboxes to be cleared.
In this case, it's `AdeleV` and `KimA`.

```powershell
$Mailboxes = @(
  "AdeleV",
  "KimA"
  )

$Mailboxes | Set-Mailbox -RetentionPolicy "Mailbox Purge Policy"
```

After the policy is applied, the Managed Folder Assistant should be triggered
to force an immediate application of the policy. Beware that AD replication
within Office 365 may mean that the Retention Policy takes around half an hour
to apply, after which point the following can be run:

```powershell
$Mailboxes | ForEach-Object { Start-ManagedFolderAssistant -Identity $_ }
```

Running this command will trigger the Managed Folder Assistant to run across 
the mailboxes in question, thus deleting the content.
