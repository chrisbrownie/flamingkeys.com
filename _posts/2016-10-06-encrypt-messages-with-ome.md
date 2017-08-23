---
layout: post
title: Encrypt Messages with Office 365 Message Encryption
date: 2016-10-06 08:38:53.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Microsoft
- Office 365
- PowerShell
- Security
meta:
  _edit_last: '1'
  amazonS3_cache: a:5:{s:101:"//static.flamingkeys.com/wp-content/uploads/2016/10/06072217/Image_O365MessageEncryption_430x2171.jpg";i:1233;s:85:"//flamingkeys.com/wp-content/uploads/2016/10/Image_O365MessageEncryption_430x2171.jpg";i:1233;s:51:"//technet.microsoft.com/en-us/library/dn569286.aspx";a:1:{s:9:"timestamp";i:1475743133;}s:66:"//gist.github.com/chrisbrownie/8ce6ffa38dcfe7d1cf513d7135bbf34c.js";a:1:{s:9:"timestamp";i:1475741684;}s:64:"//technet.microsoft.com/en-us/library/bb125138(v=exchg.160).aspx";a:1:{s:9:"timestamp";i:1475743133;}}
  _yoast_wpseo_content_score: '30'
  _yoast_wpseo_primary_category: '8'
  _syntaxhighlighter_encoded: '1'
  _oembed_57f71f7b2a7a07f591f200a1fd79b674: "{{unknown}}"
  _oembed_b60dddaa9a7afea14dbbae19be571b29: "{{unknown}}"
  _oembed_92d89f1308fcfb417d8a7ef9e6fc3862: "{{unknown}}"
  _oembed_ec6ec0325b1851b2b984aaa644af4409: "{{unknown}}"
  _oembed_fe80d01bc886bc2b5e1f7ada3c16b314: "{{unknown}}"
  _yoast_wpseo_focuskw: encryption
  _yoast_wpseo_focuskw_text_input: encryption
  _yoast_wpseo_metadesc: Office 365 Message Encryption is Office 365's way of providing
    end-to-end message encryption, without requiring any client software.
  _yoast_wpseo_linkdex: '83'
  dsq_thread_id: '5200896337'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

There are a [whole bunch](https://technet.microsoft.com/en-us/library/dn569286.aspx) of Encryption technologies in Office 365. [Office 365 Message Encryption](https://products.office.com/en-us/exchange/office-365-message-encryption) (OME) is Office 365's way of providing end-to-end message encryption, without requiring any software beyond a web browser for the receiving party. It also does not require the recipient to be running Windows, or using Outlook, or for them to be an Office 365 customer. It is easy to automate, has no certificate requirement, and allows the recipient to reply in an encrypted fashion.

![]({{ site.baseurl }}/assets/Image_O365MessageEncryption_430x2171.jpg)

# Automatic Message Encryption

Exchange Server administrators will be familiar with the concept of Transport Rules, now Mail Flow Rules in Exchange Online/Office 365. We can use Mail Flow Rules to kick off encryption when a particular condition is met. Below, we'll explore a few common scenarios.

Before you complete any of the commands below, you'll need to [Connect PowerShell to Exchange Online](http://exchangeserverpro.com/powershell-function-connect-office-365/).

## Encrypt Based on Sender's Group Membership

Say, for example, you need to encrypt all messages sent by the security team. Provided they're all in a distribution group called 'Security' we can handle this like so:

{% gist 71810b880d202c52b14efbc21fb2c8e0 %}

## Encrypt Based on Recipient Domain

If you deal with an organisation that requires all correspondence to be encrypted, this may come in handy. You can create a rule to encrypt all messages to a particular SMTP domain as follows:

{% gist d94e70af9cf98cc929c805f950f571d2 %}

## Encrypt Based on Message Sensitivity (Private/Confidential)

This solution is a mechanism that allows for easy and transparent, but selective, rollout of OME behaviour. You can advise staff that marking a message as "Private" or "Confidential" in Outlook will cause it to be encrypted. This rule looks like the following:

{% gist https://gist.github.com/chrisbrownie/83291881584c3d863a0a8c9c79d28a67 %}

## Encrypt Based on Subject

This solution encrypts messages based on the content of a subject. This means that users can enter a specific string in a message subject to have it encrypted upon send. This might be useful if you already have plugins that categorize messages (think [SEC=UNCLASSIFIED]).

{% gist bdb5ee0a41bcfe94d00bf62fb11b9653 %}

_Thanks to [this thread](https://techcommunity.microsoft.com/t5/Exchange/Transport-rule-for-message-encryption-with-regex/m-p/18772) for prompting me to have a think about this one.

### Cmdlets Used

* [New-TransportRule](https://technet.microsoft.com/en-us/library/bb125138(v=exchg.160).aspx)

