## Riddle Registry — picoMini by CMU-Africa

Category: Forensics

Difficulty: Easy

Platform: picoMini by CMU-Africa

Author: Prince Niyonshuti N

Status: Solved

Tools Used: Kali Linux, Linux CLI, ExifTool

1. Challenge Description

Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered. Find the PDF file here Hidden Confidential Document and uncover the flag within the metadata.

The challenge provided a pdf file named confidential.pdf. The objectives is to find out the hidden flag.

2. Initial Investigation
I downloaded the pdf file can examined its metadata using ExifTool. 
```bash
exiftool confidential.pdf
```

**Output**
```text
ExifTool Version Number         : 13.55
File Name                       : confidential.pdf
Directory                       : .
File Size                       : 183 kB
File Modification Date/Time     : 2026:10:14 07:42:57-07:00
File Access Date/Time           : 2026:10:14 08:09:40-07:00
File Inode Change Date/Time     : 2026:10:14 08:09:39-07:00
File Permissions                : -rw-------
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.7
Linearized                      : No
Page Count                      : 1
Producer                        : PyPDF2
Author                          : YWNhZGVteXtwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV85ZDNjYzY2OX0=
```
Dring the investigation, i noticed that the Author name abnormal

3. Identifying the Suspicious Metadata
I found out that the abnormal string value ended with an equals sign (=). 

This pattern suggested that the value might be Base64-encoded.

So, i decided to focus on decoding the value

4. Base64 Decoding
I used ExifTool to extract the Author field and piped its value into the Base64 Decoder
```bash
exiftool -s3 -Author confidential.pdf | base64 -d
```

**output**
```text
academy{puzzl3d_m3tadata_f0und!_9d3cc669}
```

The flag found!!!


## Lessons Learned
```bash
exiftool -s3
```
1. is used to display only the metadata value without the field name.
2. A value that ended with (=), it could be a Base64 encoded value.