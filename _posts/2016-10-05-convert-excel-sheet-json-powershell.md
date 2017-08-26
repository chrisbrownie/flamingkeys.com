---
layout: post
title: Convert Excel Sheet to Json with PowerShell
date: 2016-10-05 05:38:19.000000000 +11:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Microsoft
- Office 365
- PowerShell
- Scripting
- windows
meta:
  _edit_last: '1'
  amazonS3_cache: a:2:{s:65:"//flamingkeys.com/wp-content/uploads/2016/10/spreadsheetjson1.png";i:1228;s:81:"//static.flamingkeys.com/wp-content/uploads/2016/10/05052348/spreadsheetjson1.png";i:1228;}
  _yoast_wpseo_focuskw_text_input: excel
  _syntaxhighlighter_encoded: '1'
  _yoast_wpseo_focuskw: excel
  _yoast_wpseo_metadesc: This script converts a sheet from an Excel Workbook to a
    JSON file.
  _yoast_wpseo_linkdex: '85'
  _yoast_wpseo_content_score: '90'
  _yoast_wpseo_primary_category: '8'
  dsq_thread_id: '5197642290'
  _wp_old_slug: convert-excel-sheet-json-with-powershell
author:
  login: chris
  email: chris@flamingkeys.com
  display_name: Chris
  first_name: Chris
  last_name: Brown
---
This script converts a sheet from an Excel Workbook to a JSON file.

## Why?

This is useful as JSON files allow all sorts of things that would break CSV files, like commas.

![]({{ site.baseurl }}/assets/spreadsheetjson1.png)

This would not be a pleasant CSV file to deal with:

    Hostname,IPAddress,OperatingSystem
    DC01,10.0.0.11,10.0.0.15,Windows Server 2012 R2

This would look something like the following if parsed as a CSV file:

    Hostname: DC01
    IPAddress: 10.0.0.11
    OperatingSystem: 10.0.0.15
    ?: Windows Server 2012 R2

Not ideal. JSON will display it like this:

{% highlight json %}
[
  {
    "Hostname": "DC01",
    "IPAddress": "10.0.0.11,10.0.0.15",
    "OperatingSystem: "Windows Server 2012 R2"
  }
]
{% endhighlight %}

It also allows for much more complexity than this, making JSON a great format for working with complicated sets of data. This script barely scratches the surface.

# Requirements

* Windows PowerShell (v5.1 tested, please let me know what versions you've got it working on!)
* Microsoft Excel (2016 tested, as with PowerShell, please let me know what versions you've got it working on!)

## Usage

If you provide no input beyond the name of a spreadsheet, the script will confirm only sheet exists, and output the JSON file into the current directory:

{% highlight powershell %}
Convert-ExcelSheetToJson -InputFile MyExcelWorkbook.xlsx
{% endhighlight %}

You can use the -SheetName parameter to specify a sheet name to export:

{% highlight powershell %}
Convert-ExcelSheetToJson -InputFile MyExcelWorkbook.xlsx -SheetName Sheet1
{% endhighlight %}

Or, you can specify the output file as well:

{% highlight powershell %}
Convert-ExcelSheetToJson -InputFile MyExcelWorkbook.xlsx -OutputFileName MyConvertedFile.json -SheetName Sheet2
{% endhighlight %}

The script also accepts an input file from pipeline:

{% highlight powershell %}
Get-Item MyExcelWorkbook.xlsx | Convert-ExcelSheetToJson -OutputFileName MyConvertedFile.json -SheetName Sheet2
{% endhighlight %}
## Where to get it

You can find the script on:
* [GitHub](https://github.com/chrisbrownie/Convert-ExcelSheetToJson)
* [TechNet Script Gallery](https://gallery.technet.microsoft.com/Convert-ExcelSheetToJsonps1-02c8a2d8)
