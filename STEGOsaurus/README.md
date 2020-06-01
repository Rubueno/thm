# STEGOsaurus

STEGOsaurus? More like STEGOception! ;)

Room URL: https://tryhackme.com/room/stegosaurus

---

This one actually recommends using `steghide` and `stegcracker`. Let's use those
for this challenge.


# Task 1

1. What's the file name of the second file? With file extension.

When using `steghide` to extract contents from this file with `steghide extract
-sf Dogehomemadememe.jpg` it asks for a password. I've run `stegcracker` againts
this file for quite a bit without any result, so I read back at the assignment.
The hint is in "Note: we often need a passcode, but sometimes we don't."

So when submitting a blank password we retrieve an extra jpg file showing a
panda.

```
$ steghide extract -sf Dogehomemadememe.jpg
Enter passphrase:
wrote extracted data to "729785.jpg"
```

```
729785.jpg
```

2. What's the second flag?

This one actually needs to be supplied with a password to extract, so we run
`stegcracker` against it with the [rockyou.txt](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz)
password list. After running stegcracker against it for way too long I've
decided to start tweaking with the extracted image. After chaning the exposure,
we end up with what seems to be the flag.

```
save the planet
```
