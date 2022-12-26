# Malicious Document Analysis

## Office Document Formats :

**1. Structured Storage Format -** It is an older version which is used before 2007, Having extentions **".doc",".xls",".ppt"**

**2. Office Open XML Format -** After 2007 this file format is released, This format itself and archive zip file. Having extentions **".docx",".docm",".xlsx",".pptx"**

- The main difference between the two file formats is that in DOC, your document is saved in a binary file that includes all the related formatting and other relevant data while a DOCX file is actually a zip file with all the XML files associated with the document. 

- This means that you can replace the DOCX extension of the file with .ZIP and still open the document with any zip compression software.


![image](https://user-images.githubusercontent.com/43460691/209474080-5ac7393d-b4a2-4f42-a8f3-24f8d69697d7.png)

- Differance between two file formats.

![image](https://user-images.githubusercontent.com/43460691/209474371-0deb3a19-cf56-4e01-8240-d3e3a850aa47.png)

- Hierarchy of both file formats.

![image](https://user-images.githubusercontent.com/43460691/209474890-15dbc5d8-4665-479e-8ee4-d9db39687e3f.png)

## Tools used for Analysis

> **exiftool -** Used to check file Metadata.

> **Yara -** Used to detect file signature using yara rules.

> **olevba -** Used to extract and vba script analysis.

> **ViperMonkey -** ViperMonkey is a VBA Emulation engine written in Python, designed to analyze and deobfuscate malicious VBA Macros.

> **Lazy Office Analyzer -** Loffice is making use of WinAppDbg to extract URLs' from Office documents but also VB-script and Javascript.
- Use the below link to install Loffice and its requirements.
- https://github.com/tehsyntx/loffice

## Malware Sample

> **MD5:** 2264DD0EE26D8E3FBDF715DD0D807569

> **SHA256:** ad6cedb0d1244c1d740bf5f681850a275c4592281cdebb491ce533edd9d6a77d

## Tool - exiftool

> ***`REMnux: exiftool "location of baddoc.doc file"`***

- We are using this tool to check Metadata of file.

- **.dotm template** means there is a macro inside it.This office document can embed scripts inside it that can be executed.


![image](https://user-images.githubusercontent.com/43460691/209567363-096994dd-d710-4b4b-b187-70f890fed9d8.png)
![image](https://user-images.githubusercontent.com/43460691/209567454-06b39d95-ab9e-4eef-ae4e-c8ff8a4257df.png)

## Tool - Yara

> ***`REMnux: yara -w (yara rules location)~/Documents/rules-master/index.yar baddoc.doc`***

![image](https://user-images.githubusercontent.com/43460691/209569642-5792ffa7-f6aa-45cf-bfca-0bae51d35a77.png)


- We are scanning file using yara rules.

- You can check the below output,yara detect malicious macro in the file.

![image](https://user-images.githubusercontent.com/43460691/209568437-754e2ae6-1647-4689-bd04-9154bfc29f71.png)

## Tool - olevba

> ***`remnux:~/Downloads/mal_office$ olevba baddoc.doc`***

- This file contains the vba macro, as you can see from the metadata and yara analysis. To extract the vba macro for analysis, we will use the olevba tool. 

- This tool extract vba script and also provide you analysis of the vba script.

- Below is the only analysis of the olevba tool, you can check attached vba script extracted by olevba tool.

```
-------------------------------------------------------------------------------
VBA MACRO UserForm1.frm 
in file: baddoc.doc - OLE stream: 'Macros/VBA/UserForm1'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
(empty macro)
+----------+--------------------+---------------------------------------------+
|Type      |Keyword             |Description                                  |
+----------+--------------------+---------------------------------------------+
|AutoExec  |AutoOpen            |Runs when the Word document is opened        |
|AutoExec  |Auto_Open           |Runs when the Excel Workbook is opened       |
|AutoExec  |Workbook_Open       |Runs when the Excel Workbook is opened       |
|Suspicious|Environ             |May read system environment variables        |
|Suspicious|Open                |May open a file                              |
|Suspicious|Write               |May write to a file (if combined with Open)  |
|Suspicious|Output              |May write to a file (if combined with Open)  |
|Suspicious|Print #             |May write to a file (if combined with Open)  |
|Suspicious|Kill                |May delete a file                            |
|Suspicious|Shell               |May run an executable file or a system       |
|          |                    |command                                      |
|Suspicious|vbNormal            |May run an executable file or a system       |
|          |                    |command                                      |
|Suspicious|GetObject           |May get an OLE object with a running instance|
|Suspicious|Windows             |May enumerate application windows (if        |
|          |                    |combined with Shell.Application object)      |
|Suspicious|User-Agent          |May download files from the Internet         |
|Suspicious|Chr                 |May attempt to obfuscate specific strings    |
|          |                    |(use option --deobf to deobfuscate)          |
|Suspicious|system              |May run an executable file or a system       |
|          |                    |command on a Mac (if combined with           |
|          |                    |libc.dylib)                                  |
|Suspicious|Hex Strings         |Hex-encoded strings were detected, may be    |
|          |                    |used to obfuscate strings (option --decode to|
|          |                    |see all)                                     |
|Suspicious|Base64 Strings      |Base64-encoded strings were detected, may be |
|          |                    |used to obfuscate strings (option --decode to|
|          |                    |see all)                                     |
|IOC       |1.3.1.2             |IPv4 address                                 |
|IOC       |2.2.1.1             |IPv4 address                                 |
|IOC       |1.1.2.2             |IPv4 address                                 |
+----------+--------------------+---------------------------------------------+ 
```

- You can extract vba script into new vba file using below command.

> ***`remnux:~/Downloads/mal_office$ olevba -c baddoc.doc > baddoc.vba`***

- Extracated vba script is obfuscated and to deobfuscate we will use below command.

> ***`remnux:~/Downloads/mal_office$ olevba --deobf --reveal baddoc.vba > deof_baddoc.vba`***

![image](https://user-images.githubusercontent.com/43460691/209572759-98381980-db33-417b-a15e-27d5117a9ba0.png)

## Tool - ViperMonkey

> ***`remnux:~/Downloads/mal_office$ vmonkey (extracted vba)baddoc.vba`***

- We are using this tool to analyze and deobfuscate malicious VBA Macros. This tool will parsed the vba code.

``` Recorded Actions:
+--------------------+---------------------------+---------------------------+
| Action             | Parameters                | Description               |
+--------------------+---------------------------+---------------------------+
| Found Entry Point  | autoopen                  |                           |
| Auto_Open          |                           | Interesting Function Call |
| Environ            | ['username']              | Interesting Function Call |
| Delete File        | c:\Windows\Temp\adobeacd- | Kill                      |
|                    | updatexp.vbs              |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | update.bat                |                           |
| Dropped File Hash  | 9a978c585037a509a561e5ade | File Name: adobeacd-      |
|                    | 2876b1a3947dc2b4d5b41891f | update.bat                |
|                    | e556601f293b9d            |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | updatexp.vbs              |                           |
| Dropped File Hash  | cd040d91bb767d9222a0e1411 | File Name: adobeacd-      |
|                    | c55fbe3c8fb5897d1fc215a68 | updatexp.vbs              |
|                    | 39f32937a37b65            |                           |
| Execute Command    | c:\Windows\Temp\adobeacd- | Shell function            |
|                    | update.bat                |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.ps1                |                           |
| Dropped File Hash  | f7af75ee9948552e7e9a9dc8c | File Name: adobeacd-      |
|                    | 9c5f3e5f64c01cfea90f1ede0 | update.ps1                |
|                    | 13cf9138f6efc3            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.vbs                |                           |
| Dropped File Hash  | e9b16a3046c774afc3b3d2276 | File Name: adobeacd-      |
|                    | 637878e6fa822d73740867819 | update.vbs                |
|                    | 50aeb4952dfc0a            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Dropped File Hash  | 7bbb8a216527e939f0d576273 | File Name: adobeacd-      |
|                    | b96b2e98415a400229f77bf56 | update.bat                |
|                    | d8d365da4b84f7            |                           |
| Execute Command    | c:\Users\admin\AppData\Lo | Shell function            |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
| Found Entry Point  | auto_open                 |                           |
| Environ            | ['username']              | Interesting Function Call |
| Delete File        | c:\Windows\Temp\adobeacd- | Kill                      |
|                    | updatexp.vbs              |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | update.bat                |                           |
| Dropped File Hash  | 9a978c585037a509a561e5ade | File Name: adobeacd-      |
|                    | 2876b1a3947dc2b4d5b41891f | update.bat                |
|                    | e556601f293b9d            |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | updatexp.vbs              |                           |
| Dropped File Hash  | cd040d91bb767d9222a0e1411 | File Name: adobeacd-      |
|                    | c55fbe3c8fb5897d1fc215a68 | updatexp.vbs              |
|                    | 39f32937a37b65            |                           |
| Execute Command    | c:\Windows\Temp\adobeacd- | Shell function            |
|                    | update.bat                |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.ps1                |                           |
| Dropped File Hash  | f7af75ee9948552e7e9a9dc8c | File Name: adobeacd-      |
|                    | 9c5f3e5f64c01cfea90f1ede0 | update.ps1                |
|                    | 13cf9138f6efc3            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.vbs                |                           |
| Dropped File Hash  | e9b16a3046c774afc3b3d2276 | File Name: adobeacd-      |
|                    | 637878e6fa822d73740867819 | update.vbs                |
|                    | 50aeb4952dfc0a            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Dropped File Hash  | 7bbb8a216527e939f0d576273 | File Name: adobeacd-      |
|                    | b96b2e98415a400229f77bf56 | update.bat                |
|                    | d8d365da4b84f7            |                           |
| Execute Command    | c:\Users\admin\AppData\Lo | Shell function            |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
| Found Entry Point  | workbook_open             |                           |
| Auto_Open          |                           | Interesting Function Call |
| Environ            | ['username']              | Interesting Function Call |
| Delete File        | c:\Windows\Temp\adobeacd- | Kill                      |
|                    | updatexp.vbs              |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| GetObject          | ['winmgmts:{impersonation | Interesting Function Call |
|                    | Level=impersonate}!\\\\.\ |                           |
|                    | \root\\cimv2']            |                           |
| Execute Query      | Select * from             | Query                     |
|                    | Win32_OperatingSystem     |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | update.bat                |                           |
| Dropped File Hash  | 9a978c585037a509a561e5ade | File Name: adobeacd-      |
|                    | 2876b1a3947dc2b4d5b41891f | update.bat                |
|                    | e556601f293b9d            |                           |
| OPEN               | c:\Windows\Temp\adobeacd- | Open File                 |
|                    | updatexp.vbs              |                           |
| Dropped File Hash  | cd040d91bb767d9222a0e1411 | File Name: adobeacd-      |
|                    | c55fbe3c8fb5897d1fc215a68 | updatexp.vbs              |
|                    | 39f32937a37b65            |                           |
| Execute Command    | c:\Windows\Temp\adobeacd- | Shell function            |
|                    | update.bat                |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.ps1                |                           |
| Dropped File Hash  | f7af75ee9948552e7e9a9dc8c | File Name: adobeacd-      |
|                    | 9c5f3e5f64c01cfea90f1ede0 | update.ps1                |
|                    | 13cf9138f6efc3            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.vbs                |                           |
| Dropped File Hash  | e9b16a3046c774afc3b3d2276 | File Name: adobeacd-      |
|                    | 637878e6fa822d73740867819 | update.vbs                |
|                    | 50aeb4952dfc0a            |                           |
| OPEN               | c:\Users\admin\AppData\Lo | Open File                 |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Dropped File Hash  | 7bbb8a216527e939f0d576273 | File Name: adobeacd-      |
|                    | b96b2e98415a400229f77bf56 | update.bat                |
|                    | d8d365da4b84f7            |                           |
| Execute Command    | c:\Users\admin\AppData\Lo | Shell function            |
|                    | cal\Temp\adobeacd-        |                           |
|                    | update.bat                |                           |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | rrtt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
| Object.Method Call | ['NULL']                  | yytt.Collapse             |
+--------------------+---------------------------+---------------------------+
```







