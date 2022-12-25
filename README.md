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

> **Zipdump and yara -** Used to detect file signature using yara rules.

> **Lazy Office Analyzer** 

## Malware Sample

> **MD5:** 2264DD0EE26D8E3FBDF715DD0D807569

> **SHA256:** ad6cedb0d1244c1d740bf5f681850a275c4592281cdebb491ce533edd9d6a77d
