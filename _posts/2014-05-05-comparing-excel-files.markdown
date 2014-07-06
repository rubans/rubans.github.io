---
layout: post
title: "Comparing Excel files"
description: ""
category: 
tags: []
---
I sometimes have the need to compare between different versions of a CSV or Excel file. Often to compare data differences between older and newer versions of the file.

When it's just a simple CSV file then I initially used some powershell:

    Compare-Object $(Get-Content c:\Tfs\Dev\DataDiff\basesample.csv) $(Get-Content C:\Tfs\Dev\DataDiff\samplewithdiffvalues.csv)

This did the job, then I found a decent free comparer for Excel (not CSV format)
[http://www.grigsoft.com/wincmp3.htm)](http://www.grigsoft.com/wincmp3.htm))

If you don't mind paying a little extra and well worth the licence by the way for the comprehensive compare options it offers (not just Excel), Beyond Compare is great for this.

However, I recently found a free add Included as part of Excel 2013 which offers great way of visualising the differences:
[http://office.microsoft.com/en-001/excel-help/what-you-can-do-with-spreadsheet-inquire-HA102835926.aspx](http://office.microsoft.com/en-001/excel-help/what-you-can-do-with-spreadsheet-inquire-HA102835926.aspx)

It's also available from your start menu (Windows 7) or through Search Charm (Windows 8) as Speadsheet Compare 2013.
![Image]({{ site.url }}/Images/posts/SpreadsheetCompare2013.jpg)

