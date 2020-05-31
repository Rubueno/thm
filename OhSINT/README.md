# OhSINT

Are you able to use open source intelligence to solve this challenge?

---

# Task 1

What information can you possible get starting with just one photo?

1. What is this users avatar of?

The pic is the default Windows XP background. Let's run `exiftools` against it.

```
$ exiftool WindowsXP.jpg
ExifTool Version Number         : 11.85
File Name                       : WindowsXP.jpg
Directory                       : .
File Size                       : 229 kB
File Modification Date/Time     : 2020:05:31 16:07:09+02:00
File Access Date/Time           : 2020:05:31 16:08:09+02:00
File Inode Change Date/Time     : 2020:05:31 16:07:24+02:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
XMP Toolkit                     : Image::ExifTool 11.27
GPS Latitude                    : 54 deg 17' 41.27" N
GPS Longitude                   : 2 deg 15' 1.33" W
Copyright                       : OWoodflint
Image Width                     : 1920
Image Height                    : 1080
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1080
Megapixels                      : 2.1
GPS Latitude Ref                : North
GPS Longitude Ref               : West
GPS Position                    : 54 deg 17' 41.27" N, 2 deg 15' 1.33" W
```

The interesting parts here are the copyright and GPS position. Throwing the GPS
position into Google Maps returns us to [Yorkshire Dales National
Park](https://www.google.com/maps/place/54%C2%B017'41.3%22N+2%C2%B015'01.3%22W/@54.1914787,-2.0993761,8.46z/data=!4m5!3m4!1s0x0:0x0!8m2!3d54.2947972!4d-2.2503694).
Funny, I've been there.

Let's explore the copyright `OWoodflint`. It seems they have a blog, a Twitter
handle and a github repo..
- [blog](https://oliverwoodflint.wordpress.com/author/owoodflint/)
- [twitter](https://twitter.com/owoodflint)
- [github](https://github.com/OWoodfl1nt/people_finder)

The blog mentions they're in New York. The twitter page has just two tweets, one
containing a BSSID. That could be helpful. The twitter avatar is of a cat.
Question 1?  The github repo mentions they're from London and has their gmail
address.

```
cat
```

2. What city is this person in?

We found this answer in their github repo.

```
London
```

3. Whats the SSID of the WAP he connected to?

Hmm so we have their BSSID from their Twitter page: `B4:5D:50:AA:86:41`. We can
try and use `wigle.net` to see if they've indexed this BSSID. There's a single
network in London with this BSSID.

```
UnileverWiFi
```

4. What is his personal email address?

Github again

```

```

5. What site did you find his email address on?

```
Github
```

6. Where has he gone on holiday?

This one we found on their blog

```
New York
```

7. What is this persons password?

This one is a little cheeky, but the password is in a paragraph on their blog.
The paragraph is in all-white text, making it only visible by inspecting the
source code or by highlighting the paragraph.

```
<p style="color:#ffffff;" class="has-text-color">pennYDr0pper.!</p>
```

```
pennYDr0pper.!
```
