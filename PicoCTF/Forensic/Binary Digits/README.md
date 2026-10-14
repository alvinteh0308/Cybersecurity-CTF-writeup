## Binary Digits — picoCTF 2026

Category: Forensics

Difficulty: Easy

Platform: picoCTF 2026

Author: Yahaya Meddy

Status: Solved

Tools Used: Kali Linux, Linux CLI, Python 3, Pillow

1. Challenge Description:

This file doesn't look like much... just a bunch of 1s and 0s. But maybe it's not just random noise. Can you recover anything meaningful from this?

The challenge provided a file named digits.bin. The objectives is to find out the hidden messages.

2. Initial Investigation:

I started by identifying the file type using the Linux file cmd
```bash
file digit.bin
```

**Output**
```text
digits.bin: ASCII text, with very long lines (65536), with no line terminators
```
Although the file had a .bin extension, the result show that it contained ASCII text rather that a recognised binary file format.

Next, I inspected the first 100 bytes
```bash
head -c 100 digits.bin
```
The output contained a long sequence of 0 and 1 characters

Then i checked the file size:
```bash
wc -c digits.bin
```

**Output**
```text
71424 digits.bin
```

And i verify whether the file contain others characters instead of 0 and 1
```bash
grep -o '[^01]' digits.bin | sort -u
```
No output was returned

3. First Attempt - Binary Image Reconstruction:

Initially, i suspected that the binary file might represent black and white pixels

Since the file contained 71424 characters, So i think it could be an image dimension of 248 x 288 pixels

So i use python and pillow to convert the digits into grayscale pixel values:
``` python
from PIL import Image
data = open("digits.bin", "r").read()
pixels = [int(bit) * 255 for bit in data]
img = Image.new("L", (248, 288))
img.putdata(pixels)
img.save("recovered.png")
```
But, the generated image contained mostly black and white noise, without any recognisable text or flag

4. Second Attempt - Binary to Bytes Conversion:

After first attempt failed, i decided to invstigate whether the binary sigits can be converted into bytes using groups of eight bits

I used python
```python
data = open("digits.bin", "r").read()

binary_bytes = [
    int(data[i:i+8], 2)
    for i in range(0, len(data), 8)
]

print(bytes(binary_bytes)[:100])
```

The output started with
**Output**
```text
b'\xff\xd8\xff\xe0\x00\x10JFIF'
```
This gave me an inportant messages. The hexadecimal values FF D8 FF E0, followed by JFIF, indicated that the decoded bytes likely represented a JPEG image.

5. Rcovering the JPEG file:

After identifying the JPEG file, i wrote the decoded bytes into a new .jpg file
```python
with open("recovered.jpg", "wb") as f:
    f.write(bytes(binary_bytes))
```
Then i open recovered image
```bash
xdg-open recovered.jpg
```

The image opened successfully and displayed the hidden flag

## Lessons Learned
Throught this challenge, i learned
1. File identification: A file extension does not always represent its actual content
2. Data Inspection: Commands such as file, head, wc, and grep are useful for initial investigation
3. Binary Encoding: A sequence of 0 and 1 characters can represent the individual bits of an encoded file
4. File Signature Analysis: bytes such as FF D8 FF E0 can help identify a JPEG file
5. Problem Solving: An unsuccessful approach can stll provide useful information and help guide the next investigation step.

| File Type	| 常见 File Signature（Hex）|
|---|---|
| JPEG | FF D8 FF |
| PNG | 89 50 4E 47 0D 0A 1A 0A |
| PDF |	25 50 44 46 (%PDF) |
| ZIP |	50 4B 03 04 |
| GIF |	47 49 46 38 (GIF8) |