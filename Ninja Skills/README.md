# Ninja Skills

Practise your Linux skills and complete the challenges.

Room URL: https://tryhackme.com/room/ninjaskills

---

Answer the questions about the following files:

8V2L
bny0
c4ZX
D8B3
FHl1
oiMO
PFbD
rmfX
SRSq
uqyw
v2Vb
X1Uy
The aim is to answer the questions as efficiently as possible.

---

# Task 1

1. Which of the above files are owned by the best-group group(enter the answer separated by spaces in alphabetical order)

SSH'ing into the machine shows a folder `files` but it's empty. So let's try and
find those files. I've created a `files.txt` file containing all the files we're
looking for. Know that there's several ways to find these files, you could just
do a single long `find / -name name1 -o name name2 ...`. I couldn't actually
find the file `bny0`. I'm not sure why that is.

```bash
$ for file in $(cat files.txt); do find / -type f -name $file -exec ls {} >> files2.txt +; done
```

Now that we have the `files2.txt` we can use these to do the tasks.

```bash
for file in $(cat files2.txt); do find $file -group best-group; done
/mnt/D8B3
/home/v2Vb
```

```
D8B3 v2V
```

2. Which of these files contain an IP address?

Google helped us find a simple IP address regex (which actually matches all
occurrences of `0.0.0.0` to `999.999.999.999` but I think it wouldn't mind for
this task.

```bash
$ for file in $(cat files2.txt); do echo $file; grep -E "([0-9]{1,3}[\.]){3}[0-9]{1,3}" $file; done
/etc/8V2L
/mnt/c4ZX
/mnt/D8B3
/var/FHl1
/opt/oiMO
wNXbEERat4wE0w/O9Mn1.1.1.1VeiSLv47L4B2Mxy3M0XbCYVf9TSJeg905weaIk
/opt/PFbD
/media/rmfX
/etc/ssh/SRSq
/var/log/uqyw
/home/v2Vb
/X1Uy
```

```
oiMO
```

3. Which file has the SHA1 hash of 9d54da7584015647ba052173b84d45e8007eba94

Easy.

```bash
$ for file in $(cat files2.txt); do sha1sum $file | grep 9d54da7584015647ba052173b84d45e8007eba94; done
9d54da7584015647ba052173b84d45e8007eba94  /mnt/c4ZX
```

```
c4ZX
```

4. Which file contains 230 lines?

Condsidering none of the files contain 230 lines I went on a limb and submitted
the filename of the missing file, which turned out to be the answer.

```bash
$ for file in $(cat files2.txt); do cat $file | wc -l | grep 230; done
$ for file in $(cat files2.txt); do cat $file | wc -l ; done
209
209
209
209
209
209
209
209
209
209
209
```

```
bny0
```

5. Which file's owner has an ID of 502?

```bash
$ for file in $(cat files2.txt); do find $file -uid 502; done
/X1Uy
```

```
X1Uy
```

6. Which file is executable by everyone?

```bash
$ for file in $(cat files2.txt); do find $file -executable; done
/etc/8V2L
```

```
8V2L
```
