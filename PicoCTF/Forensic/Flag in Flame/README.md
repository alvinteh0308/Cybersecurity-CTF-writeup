## Binary Digits — picoCTF 2026

Category: Forensics

Difficulty: Easy

Platform: picoMini by CMU-Africa

Author: Prince Niyonshuti N.

Status: Solved

Tools Used: Kali Linux, Linux CLI, xxd, base64, xdg-open

1. Challenge Description
The SOC team discovered a suspiciously large log file after a recent breach. When they opened it, they found an enormous block of encoded text instead of typical logs. Could there be something hidden within? Your mission is to inspect the resulting file and reveal the real purpose of it. The team is relying on your skills to uncover any concealed information within this unusual log. Download the encoded data here: Logs Data
. Be prepared—the file is large, and examining it thoroughly is crucial .

The challenge provided a txt file named logs. The objectives is to find out the hidden messages.

2. Initial File Investigation
I started investigate the file using tool "Exiftool"
```bash
exiftool logs.log
```

**Output:**
```text
ExifTool Version Number         : 13.55
File Name                       : logs.txt
Directory                       : .
File Size                       : 1592 kB
File Modification Date/Time     : 2026:10:23 21:05:08-07:00
File Access Date/Time           : 2026:10:23 21:16:31-07:00
File Inode Change Date/Time     : 2026:10:23 21:16:31-07:00
File Permissions                : -rw-------
File Type                       : TXT
File Type Extension             : txt
MIME Type                       : text/plain
MIME Encoding                   : us-ascii
Newlines                        : (none)
Line Count                      : 1
Word Count                      : 1
```
throught these output i found out that this logs is abnormal, because normally a logs file must contain ip addtess, event description, timestamps, etc. But the result show only 1 line count and 1 word count.

To inspect the contents without displaying entire file, i use cmd:
```bash
head -c 200 logs.txt
```

**Output:**
```text
iVBORw0KGgoAAAANSUhEUgAAA4AAAASACAIAAAAh8bSOAAEAAElEQVR4nOz919MsyZUniP3OcY+IFJ+6ouqWBqoautDYRmN6emfaZmlc0mi2+7xPNJJ/GJ/4RvJtH5dmQ1vbHu4Mp9kzjQYGohuoAkrh1tWfysyIcPdz+HAiIj3Vp66oanHs2nczIyM8XB4t6PUP/i8A 
```
These output show that the beginning start with "IVBORw0.." commonly is a png file encoded with base64.

3. Identifying the file signature
Before i decode entire file, i double check the file signature to prove that this file is png file encoded by base64. I use cmd:
```bash
head -c 100 logs.txt | base64 -d | xxd
```

**Output:**
```text
00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
00000010: 0000 0380 0000 0480 0802 0000 0021 f1b4  .............!..
00000020: 8e00 0100 0049 4441 5478 9cec fdd7 d32c  .....IDATx.....,
00000030: c995 2788 fdce 718f 8814 9fba a2ea 9606  ..'...q.........
00000040: aa1a bad0 d846 637a 7a67 da              .....Fczzg.
```
Through these, i very sure that this is a png file, because it start with "89 50 4e 47...".

4. Recover to png file
To recover into png file, i use cmd:
```bash
base64 -d logs.txt > recovered.png
```


5. Investigate PNG file
Then i open the recovered.png using xdg tool.
```bash
xdg-open recovered.png
```
![recovered.png](Resources/recovered.png)

Then i noticed in this image, there was a single line in the bottom with a string value "61636164656D797B666F72656E736963735F616E616C797369735F69735F616D617A696E675F32303433646264317D". It look like a value encoded with base64

6. Decode the string value
Then i tried to decode the string using cmd:
```bash
echo "61636164656D797B666F72656E736963735F616E616C797369735F69735F616D617A696E675F32303433646264317D" | xxd -r -p
```

**Output:**
```text
academy{forensics_analysis_is_amazing_2043dbd1}
```
Then the flag was found!!!

## Lessons Learned
1. i learned that the string value start with "iVBORw0K...", i could be png file format encded with base64
2. cmd ">" i use to save as format. Example: 
```bash
base64 -d logs.txt > recovered.png
```