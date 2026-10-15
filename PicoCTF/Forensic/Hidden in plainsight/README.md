## Hidden in plainsight — picoMini by CMU-Africa

Category: Forensics

Difficulty: Easy

Platform: picoMini by CMU-Africa

Author: Yahaya Meddy

Status: Solved

Tools Used: Kali Linux, Linux CLI, steghide

1. Challenge Description
You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag. 

The challenge provided a JPG image named img.jpg. The objectives is to find out the flag.

2. Inicial Investigation
I started using ExifTool to identify any unusual MetaData
```bash
exiftool img.jpg
```

**output**
```text
ExifTool Version Number         : 13.55
File Name                       : img.jpg
Directory                       : .
File Size                       : 74 kB
File Modification Date/Time     : 2026:10:14 18:13:57-07:00
File Access Date/Time           : 2026:10:14 18:14:53-07:00
File Inode Change Date/Time     : 2026:10:14 18:14:53-07:00
File Permissions                : -rw-------
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9
Image Width                     : 640
Image Height                    : 640
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 640x640
Megapixels                      : 0.410
```
Based on these output, i found out an unsual metadata in "Comment" field. 

3. Identifying the suspicious metadata
This metadata can be devide by '4' although the metadata ended without sign(=), but i first suspect this metadata is base64 encoded. So i tried decode this unusual value using base64 decorder.

4. First Base64 Decoding
Since i suspected the metadata contained Base64-encoded information. i extracted the metadata using Exiftool and piped the result into base64 decoder
```bash
exiftool -s3 -comment img.jpg | base64 -d
```

**output**
```text
steghide:cEF6endvcmQ=
```
This output give me two important clues:
First: the word 'steghide', this is a kali tool that used to hide and extract data from image and audio files
Second: the value 'cEF6endvcmQ=' is to be Base64 encoded

5. Second Base64 Decoding
I decode 'cEF6endvcmQ=' using cmd:
```bash
echo 'cEF6endvcmQ=' | base64 -d
```

**output**
```text
pAzzword
```
Since the first decoded message mentioned Steghide, i susoected that pAzzword was the passphrase required to access the hidden data.

6. Investigating Hidden Data with Steghide
Next, i used Steghide to inspect the image for embedded information
```bash
steghide info img.jpg
```

**output**
```text
"img.jpg":
  format: jpeg
  capacity: 4.0 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "flag.txt":
    size: 34.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```
during investigate info of img.jpg using steghide, the tool required me to input passphrase, then i use 'pAzzword' decoded just now and found out the JPEG contained a hidden file named flag.txt

7. Extract the hidden file
So i using this command to extract the hidden file.
```bash
steghide extract -sf img.jpg
```

**output**
```text
Enter passphrase: 
wrote extracted data to "flag.txt".
```
Then i check the extracted file
```bash
cat flag.txt
```

**output**
```text
academy{h1dd3n_1n_1m4g3_45cfe9c1}
```
the flag found!!!


## Lessons Learned
1. a base64 encoded value 'c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9' not neccessary need sign(=) at the end, atleast the value can be devide by '4'
2. 'steghide' is a kali tool that used to hide and extract data from image and audio files, it may need passphrase to extract hidden file