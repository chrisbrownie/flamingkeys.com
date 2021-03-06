---
layout: post
title: What is ImmutableID in Azure AD?
date: 2016-08-31 11:49:28.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Active Directory
- Azure AD
- Microsoft
- Office 365
- PowerShell
- Scripting
- Security
meta:
  _edit_last: '1'
  _yoast_wpseo_content_score: '30'
  _yoast_wpseo_linkdex: '77'
  _yoast_wpseo_primary_category: '8'
  _syntaxhighlighter_encoded: '1'
  _yoast_wpseo_focuskw: immutableID
  _yoast_wpseo_focuskw_text_input: immutableID
  dsq_thread_id: '5108571926'
  amazonS3_cache: a:1:{s:57:"https://technet.microsoft.com/en-au/library/cc961625.aspx";a:1:{s:9:"timestamp";i:1479706925;}}
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
redirect_from:
  - /2016/08/immutableid-azure-ad/
---
Let's talk about ImmutableID.
Immutable is a funny word. If you ask the dictionary, it'll tell you that it means something like "unchanging over time" or "unchangeable". If you ask Microsoft, prior to about 2014, they'd have agreed with these definitions.

# Definitions

Before we get started, let's define a few things:

| Term | Definition |
| --- | --- |
| Active Directory Domain Services (AD) | This is your on-premises directory service where objects are "mastered". That is to say that the official "single source of truth" for anything we're concerned about here is AD. You may have another product that feeds into AD, but we'll treat whatever we see in AD as gospel |
| Azure Active Directory (AAD) | This is the directory behind Office 365. Any object that exists in Office 365 (think user, group, contact, etc.) resides in AAD. It's not exactly Active Directory, but it also kind of is. This isn't really relevant, we just care that it holds all the information and behaves somewhat like active directory |
| AAD Connect, AADSync, DirSync | This is the magic glue. AAD Connect, formerly AADSync, formerly DirSync. AAD Connect speaks to both AD and AAD and works out what changes need to be made in each, if any. If an object is created in AD within the scope of AAD Connect, AAD Connect will create that object in AAD. If an object is updated in AD within the scope of AAD Connect, AAD Connect will update the object accordingly in AAD. There are a number of write-back scenarios available (password, device, group, etc.) but, for the most part, this synchronization is one-directional |

# Understanding primary keys

In a relational database, primary keys are used to relate a record in one table with a record in one or more other tables. For instance, there may be a table called `Buildings` that has a record for each building within a company, then another table called `MeetingRooms`

The `Buildings` table has a bunch of rows that look like this:

| BuildingID | BuildingName
| --- | ---
| 1 | Main Building
| 2 | Old Building


The `MeetingRooms` table looks like this:

| RoomID | RoomName | BuildingID
| --- | --- | ---
| 1 | Party Room | 1
| 2 | Celebration Room | 2
| 3 | Kitchen | 2

As you can see, based on the `BuildingID` value attached to each room specifics which building the room exists in. This allows for easy modifications to building attributes without having to update every single room (for example, if a building was renamed). As the ID fields here are referred to in multiple places, they must be configured as not only unique, but also immutable, or read-only once set. This means that if some bright spark tries to change the `Old Building` `BuildingID` to `300`, for example, all previous rooms aren't orphaned (pointing to a building record that no longer exists, because the ID they refer to no longer exists in the database).

# Unique Identifiers in Active Directory

While Active Directory has no "primary key" field like we've demonstrated above, it has a number of fields with values that are expected to never change. The one most are familiar with is the Security Identifier, or SID. This is a hexadecimal attribute that is displayed looking something like `S-1-5-21-12345-1234-1234-500`. SIDs are unique to the Active Directory forest, and are assigned only to user and group objects. Contacts and other object types do not receive a SID. SIDs are also subject to change when a user is migrated from one Active Directory domain to another within a forest.

Another attribute that can be utilised to uniquely identify an Active Directory object is the GUID. GUIDs are used extensively in the software development world to provide, in theory, a value that is globally unique across all systems and platforms. That is to say that, in all Active Directory forests around the globe, there should be no instance in which two objects have the same GUID. The same cannot be said for SIDs.

For this reason, `objectGuid` is commonly used in modern applications to uniquely identify Active Directory objects. The `objectGuid` remains with the user for as long as it lives in a particular Active Directory forest.

# AAD Connect in a greenfield world

Let's pretend, for a moment, that you've started a company called Contoso. You've set up your `contoso.com` Active Directory, and have decided that you want to use SharePoint Online within Office 365 for your intranet. Good work, you're partway there. Now you have an Office 365 tenant with a bunch of licences waiting for users, and a bunch of users in AD waiting to collaborate.

To get the users from AD into AAD, we need to deploy AAD Connect. AAD Connect is installed on a dedicated server (not a domain controller, not an AD FS server, not a file server, not a print server, an AAD Connect server), and the configuration wizard run, accepting defaults for everything.
The relevant settings from the wizard look vaguely like this:

* Synchronise all users, groups, and contacts from the root of contoso.com and below
* Use objectGuid attribute to uniquely identify users

The following workflow now takes place for every single user, contact, and group object in Active Directory:

1. AAD Connect finds user with User Principal Name `areid@contoso.com` (`CONTOSO\areid`)
2. AAD Connect takes the user's objectGuid and converts it to a Base64 string
    ```
    objectGuid: 7754d487-1fc3-4206-9e95-ce012f1586e5
    objectGuid.ToBase64(): h9RUd8MfBkKelc4BLxWG5Q==
    ```

3. AAD Connect searches its copy of AAD's information for an AAD user object with an immutableId value of `h9RUd8MfBkKelc4BLxWG5Q==`

  1. If a match is found the object is updated accordingly
  2. If a match is not found the object is created, stamping its immutableId field with the value determined above, tying the AD object to new the AAD object

There are a couple of additional scenarios that occur too:

1. If a match is not found based on `immutableID`, but a user with a matching UPN/email address and a blank `immutableId` is found, the user will have its `immutableId` stamped with the new value (determined above) and the AD+AAD users become tied together. This is called a **soft match**

2. If a match is not found based on `immutableId`, but a user with a matching UPN/email address is found but with a different `immutableId`, a sync error is thrown. AAD Connect will not override the user's UPN, as they're not the same object

3. If a match is found based on immutableId, but the object types are different (e.g. AD user matching an AAD Connect contact), AAD Connect will throw an error and refuse to synchronize the objects. This won't happen in the wild unless someone unintentionally (or intentionally) breaks something

# The problem

With an understanding of how users are created and matched in AAD, and between AD and AAD, it is hopefully now becoming apparent that in the event of a user being moved from one forest to another, or deleted and recreated with the same details, AAD Connect will treat the new user as non-existent, and will commence the decommission process if the old "master" object is missing. Not necessarily ideal.

Before about mid-2014, there was no way to resolve this. If a user had to be moved from one forest to another, their Azure AD object, and as a result mailbox and other associated objects had to be recreated. Fortunately, now, Microsoft have provided functionality to resolve it, by allowing administrators to both clear and write to the `immutableId` attribute. There's a couple of approaches to this, that I call the "safe way" and the "not-so-safe way". I typically take the safe approach, as it's much lower risk than the not-so-safe way.

NB: Both of these steps require a connection to Azure AD PowerShell, so make sure you've installed it and connected with `Connect-MsolService` before trying any of the below.

## The not-so-safe way

As the safe way is only a few more steps on top of the not-so-safe way, we'll start with this one. Put simply, we're going to wipe the `immutableId` attribute so that AAD thinks this user has never been synchronised to an on-premises "master" object before. Basically this means that any AAD Connect sync will hit step A above (no match found, `immutableId` blank) and soft match based on UPN/email address.

Here's the command to do this, you can run it for one user at a time, or for a few users, or for all users:

{% highlight powershell %}
# For one user
Get-MsolUser -UserPrincipalName areid@contoso.com | Set-MsolUser -ImmutableId $null

# for a few users (in users.txt, one UPN per line)
Get-Content users.txt | % { Get-MsolUser -UserPrincipalName $_ | Set-MsolUser -ImmutableId $null }
# For all users (probably not recommended)
Get-MsolUser -All | Set-MsolUser -ImmutableId $null
{% endhighlight %}

This will clear immutableId and make AAD think these users are "In Cloud" principles, rather than synchronised principles, which means it'll happily soft match them against AD as soon as it can.

## The safe way

While slightly more involved to get going, "the safe way" is my recommended approach. Rather than taking a scattergun to your whole environment, this allows for forceful attribution of one object to another (the astute will notice how this mechanism also allows matching users that have absolutely nothing in common, which is useful rarely).
This approach takes a little bit longer than the last method, and a smidge more PowerShell-fu, but it is well and truly worth the extra effort, in my opinion. Since we can calculate what an `immutableId` should look like, there's nothing stopping us from simply stamping an AAD object with the expected `immutableId`, making AAD Connect figure it's been syncing those users all along. This approach looks like this:

* Import the AD module to allow retrieval of AD attributes
{% highlight powershell %}
Import-Module Active Directory
{% endhighlight %}

* Grab the GUID of our test user and transform it into an immutableId
{% highlight powershell %}
$userUPN = "areid@contoso.com"
$guid = [guid]((Get-ADUser -LdapFilter "(userPrincipalName=$userUPN)").objectGuid)
$immutableId = [System.Convert]::ToBase64String($guid.ToByteArray())
{% endhighlight %}

* Write that immutableId back to the AAD user
{% highlight powershell %}
C:\> Get-MsolUser -UserPrincipalName $userUPN | Set-MsolUser -ImmutableId $immutableId
{% endhighlight %}

If you're feeling game and want to do this in bulk, the following should work too:

{% highlight powershell %}
Get-Content users.txt | % { "Set-MsolUser -UserPrincipalName $_ -ImmutableId $([System.Convert]::ToBase64String((Get-AdUser -LdapFilter "(userPrincipalName=$_)")[0].objectGuid.ToByteArray())) " }
{% endhighlight %}

Whichever method you choose, hit go in your AAD Connect instance (or, more likely, wait for the sync task to run), and it will go and tie up all these users just like a bought one.

# Further Reading

* [TechNet: SID vs GUID](https://technet.microsoft.com/en-au/library/cc961625.aspx)
* [Integrating your on-premises identities with Azure Active Directory] (https://azure.microsoft.com/en-us/documentation/articles/active-directory-aadconnect/)
* [How to use SMTP matching to match on-premises user accounts to Office 365 user accounts for directory synchronization](https://support.microsoft.com/en-au/kb/2641663)
