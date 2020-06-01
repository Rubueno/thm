# Easy steganography

An easy steganography challenge. No hint, just solve it.

Room URL: https://tryhackme.com/room/easysteganography

---

# Task 1

Download the zip file and start the hunt. Good luck!

1. Download the zip file

The zip file cointains 4 seemingly similar jpegs.

```
No answer needed.
```

2. Flag 1

Knowing the flag is 7 characters long, running `strings -n 7 flag1.jpeg` yields a
flag.

```
St3g4n0
```

3. Flag 2

`strings -n 9 flag2.jpeg` didn't yield any useful info. `binwalk` lets us know
there's 3 types of data in this file.

```
$ binwalk --dd='.*' flag2.jpeg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, EXIF standard
12            0xC             TIFF image data, little-endian offset of first image directory: 8
78447         0x1326F         JPEG image data, JFIF standard 1.01
```

The odd one out is the one at `0x1326F`. Let's open it with any available
picture viewer. I've opened it and it yields the flag.

```
ALGORITHM
```

4. Flag 3

Running `file` against this file reveals a comment:
```
$ file flag3.jpeg
flag3.jpeg: JPEG image data, Exif standard: [TIFF image data, little-endian, direntries=0], comment: "The passphrase to this challenge is Math", progressive, precision 8, 526x263, components 3
```

Considering the length of the flag is only 4 characters, this must be the flag.

```
Math
```

5. Flag 4

`binwalk` again is the helpful one here.

```
 binwalk --dd=".*" flag4.jpeg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, EXIF standard
12            0xC             TIFF image data, little-endian offset of first image directory: 8
78447         0x1326F         XML document, version: "1.0"
```

Upon inspection of the XML document, I found it was in Chinese, but one of the
last words in the file is the flag.

```
TryHardered
```
