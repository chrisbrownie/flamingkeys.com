---
layout: post
title: How to Export a List of Office 365 Service Plans
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

{% gist 5aa75b906e871339f4a8d5260c593bde %}

## Create a retention policy and assign the 1 Day Delete Tag

This creates a policy called *Mailbox Purge Policy* and assigns the 
`1 Day Delete` tag to it.

{% gist 2b7ec102e469c2b9b4eea180bbe591fb %}

## Assign the retention policy to the mailboxes to be purged

Let's assign the *Mailbox Purge Policy* policy to the mailboxes to be cleared.
In this case, it's `AdeleV` and `KimA`.

{% gist a5dd7b71a2f48d7bd10d13cc43436bd6 %}

After the policy is applied, the Managed Folder Assistant should be triggered
to force an immediate application of the policy. Beware that AD replication
within Office 365 may mean that the Retention Policy takes around half an hour
to apply, after which point the following can be run:

{% gist cc9e97e28df4c17f86ce07ecce4fef64 %}

Running this command will trigger the Managed Folder Assistant to run across 
the mailboxes in question, thus deleting the content.