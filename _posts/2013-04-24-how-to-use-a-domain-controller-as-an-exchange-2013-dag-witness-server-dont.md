---
layout: post
title: How to use a Domain Controller as an Exchange 2013 DAG Witness Server (Don’t)
date: 2013-04-24 09:29:45.000000000 +10:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AD-DS
- Email
- Exchange
- Exchange 2013
- Security
meta:
  dsq_thread_id: '1231911266'
  _edit_last: '1'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
If you’re setting up Exchange 2013 in a lab, you may have a requirement to use a domain controller as a File Share Witness (FSW) host. 
Obligatory disclaimer: While this is definitely not recommended practise in a production environment, and may have undesirable results, it will *probably* work. I highly doubt Microsoft support this, though I have been unable to find any concrete evidence on it (frankly, it’s such terrible practise they shouldn’t have to publicise advice against it). If you can come up with a method to avoid following this guide in production, I implore you to do so.

<aside class="aside-error">Do not do this in production!</aside>

The File Share Witness is used as the winning vote when your DAG has an even number of hosts – the same principle as the quorum drive in a Failover Cluster. This is a file server that all DAG members can read and write (but cannot be a DAG member for obvious reasons).
If you just try to create a DAG and use a domain controller as a FSW, you’ll get this error message:

![]({{ site.baseurl }}/assets/image_thumb.png)

Checking the log file gives the following access denied message, which is less than helpful:

    [2013-04-23T23:15:32] WriteError! Exception = System.Management.ManagementException: Access denied
     at System.Management.ManagementException.ThrowWithExtendedInfo(ManagementStatus errorCode)         
      at System.Management.ManagementScope.InitializeGuts(Object o)         
       at System.Management.ManagementScope.Initialize()         
        at Microsoft.Exchange.Management.Common.WmiWrapper.TryGetSystemDrive(String computerFqdn)         
         at Microsoft.Exchange.Management.Common.FileShareWitness.Initialize()         
          at Microsoft.Exchange.Management.SystemConfigurationTasks.NewDatabaseAvailabilityGroup.InternalValidate()

![](https://www.flamingkeys.com/wp-content/uploads/2013/04/image1.png)

What’s happening here is that the Exchange server is trying to create a file share on the FSW host, in this case **E15DC1**. In order to allow this functionality on a domain controller, you must add it to the **Exchange Trusted Subsystem **group. This is an Active Directory group used internally by Exchange for reading/writing this share, amongst other things. Typically it will only contain Exchange Servers, but go ahead and add your file share witness host to it as well:

![](https://www.flamingkeys.com/wp-content/uploads/2013/04/image2.png)

Now comes the bit that makes the security part of me cringe. Exchange Trusted Subsystem (i.e. your Exchange servers) need to be able to create shares on this server and read/write them. As a domain controller has no local security groups, you need to apply this change directory-wide. Add *Exchange Trusted Subsystem* to your Administrators group:

What you’ve done here is made every Exchange server a domain admin. This means that anything running in the context of an Exchange server computer’s user account (`E15MB1$`, `E15MB2$`, etc.) has full rights over the domain. 

<aside class="aside-error">Do not do this in production!</aside>

After giving your DC a reboot (to update its security group membership), **Exchange Trusted Subsystem **will be able to create shares on it, and your DAG will be able to be created successfully:

![](https://www.flamingkeys.com/wp-content/uploads/2013/04/image3.png)

Add some member servers and Bob’s your uncle. Did I mention don't do this in production?

<aside class="aside-error">Do not do this in production!</aside>
