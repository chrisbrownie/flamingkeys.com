---
layout: post
title: Change or Move Sophos PureMessage SQL Database
date: 2011-02-23 10:46:38.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Anti-Virus
- AntiVirus
- Sophos
- SQL
- windows
meta:
  _edit_last: '1'
  robotsmeta: index,follow
  _sexybookmarks_shortUrl: http://fla.im/e60PLA
  _wp_old_slug: ''
  _sexybookmarks_permaHash: 72911721b191100979eba561eb0dca6f
  dsq_thread_id: '238804638'
  _jd_tweet_this: ''
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: ''
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---

Migrating [Sohos PureMessage](http://www.sophos.com/products/enterprise/email/security-and-control/microsoft-exchange/) to another SQL database is a simple process. On the server you wish to modify, open `C:\Program Files\Sophos\PureMessage\Config\LastKnown\LocalConfig.xml`. 

Under the `<LocalConfig>` tag there are four `DBSettings` tags, one for each database. These are called `ConfigDBSettings`, `QuarDBSettings`, `ReportingDBSettings` and `DirectoryDBSettings`. Inside each of these is another called `<DatabaseName>`. By modifying this `DatabaseName` you are able to change where Sophos PureMessage stores its data. This is useful if using an SQL server with more than a few databases. I like to prefix all of my Sophos PureMessage databases with “PM” to make them stand out. An extract of my completed config file looks like this:

{% highlight xml %}
<LocalConfig>
	<ConfigDataVersion>2</ConfigDataVersion>
	<Capabilities>23</Capabilities>

	<ConfigDBSettings>
		<ServerName>vsql01</ServerName>
		<DatabaseName>PMSavexCnfg</DatabaseName>
		<AuthType>0</AuthType>
		<UserName></UserName>
		<Password></Password>
		<ConnectionTimeOut>30</ConnectionTimeOut>
		<CommandTimeOut>60</CommandTimeOut>
	</ConfigDBSettings>

	<QuarDBSettings>
		<ServerName>vsql01</ServerName>
		<DatabaseName>PMSavexQuar</DatabaseName>
		<AuthType>0</AuthType>
		<UserName></UserName>
		<Password></Password>
		<ConnectionTimeOut>30</ConnectionTimeOut>
		<CommandTimeOut>60</CommandTimeOut>
	</QuarDBSettings>

	<ReportingDBSettings>
		<ServerName>vsql01</ServerName>
		<DatabaseName>PMSavexRprt</DatabaseName>
		<AuthType>0</AuthType>
		<UserName></UserName>
		<Password></Password>
		<ConnectionTimeOut>30</ConnectionTimeOut>
		<CommandTimeOut>60</CommandTimeOut>
	</ReportingDBSettings>

	<DirectoryDBSettings>
		<ServerName>vsql01</ServerName>
		<DatabaseName>PMSavexDir</DatabaseName>
		<AuthType>0</AuthType>
		<UserName></UserName>
		<Password></Password>
		<ConnectionTimeOut>30</ConnectionTimeOut>
		<CommandTimeOut>60</CommandTimeOut>
	</DirectoryDBSettings>
</LocalConfig>
{% endhighlight%}